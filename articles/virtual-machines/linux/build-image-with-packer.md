---
title: Erstellen von Linux-Azure-VM-Images mit Packer | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Packer Images von virtuellen Linux-Computern in Azure erstellen
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/18/2017
ms.author: iainfou
ms.openlocfilehash: 1752d2e0a497bf94309a744562cf4462866d6f99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Erstellen von Images von virtuellen Linux-Computern in Azure mit Packer
Jeder virtuelle Computer (VM) in Azure wird anhand eines Images erstellt, das die Linux-Distribution und -Betriebssystemversion bestimmt. Images können vorinstallierte Anwendungen und Konfigurationen enthalten. Azure Marketplace enthält viele Images von Erst- und Drittanbietern für die gängigsten Distributionen und Anwendungsumgebungen. Sie können jedoch auch entsprechend Ihren Anforderungen eigene benutzerdefinierte Images erstellen. In diesem Artikel erläutert, wie Sie mit dem Open-Source-Tool [Packer](https://www.packer.io/) benutzerdefinierte Images in Azure definieren und erstellen.


## <a name="create-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe
Während des Buildprozesses zum Erstellen der Quell-VM erstellt Packer temporäre Azure-Ressourcen. Zum Erfassen dieser Quell-VM zur Verwendung als Image müssen Sie eine Ressourcengruppe definieren. Die Ausgabe des Packer-Buildprozesses wird in dieser Ressourcengruppe gespeichert.

Erstellen Sie mit [az group create](/cli/azure/group#create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Erstellen von Azure-Anmeldeinformationen
Packer authentifiziert sich bei Azure mithilfe eines Dienstprinzipals. Ein Azure-Dienstprinzipal ist eine Sicherheitsidentität, die Sie mit Apps, Diensten und Automatisierungstools wie Packer verwenden können. Sie steuern und definieren die Berechtigungen hinsichtlich der Vorgänge, die der Dienstprinzipal in Azure ausführen können soll.

Erstellen Sie mit [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) einen Dienstprinzipalnamen, und geben Sie die Anmeldeinformationen aus, die Packer benötigt:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Ein Beispiel der Ausgabe der vorherigen Befehle lautet wie folgt:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Zur Authentifizierung bei Azure müssen Sie auch Ihre Azure-Abonnement-ID mit [az account show](/cli/azure/account#show) abrufen:

```azurecli
az account show --query "{ subscription_id: id }"
```

Die Ausgabe dieser beiden Befehle verwenden Sie im nächsten Schritt.


## <a name="define-packer-template"></a>Definieren der Packer-Vorlage
Um Images zu generieren, erstellen Sie eine Vorlage als JSON-Datei. In der Vorlage definieren Sie Generatoren und Provisioners (Bereitstellungsmethoden), die den tatsächlichen Buildprozess ausführen. Packer bietet einen [Provisioner für Azure](https://www.packer.io/docs/builders/azure.html), mit dem Sie Azure-Ressourcen definieren können, wie z.B. die Anmeldeinformationen des Dienstprinzipals, die Sie im vorherigen Schritt erstellt haben.

Erstellen Sie eine Datei namens *ubuntu.json*, und fügen Sie den folgenden Inhalt ein. Geben Sie eigene Werte für Folgendes ein:

| Parameter                           | Bezugsquelle |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Erste Zeile der Ausgabe des Erstellungsbefehls `az ad sp`: *appId* |
| *client_secret*                     | Zweite Zeile der Ausgabe des Erstellungsbefehls `az ad sp`: *password* |
| *tenant_id*                         | Dritte Zeile der Ausgabe des Erstellungsbefehls `az ad sp`: *tenant* |
| *subscription_id*                   | Ausgabe des Befehls `az account show` |
| *managed_image_resource_group_name* | Name der Ressourcengruppe, die Sie im ersten Schritt erstellt haben |
| *managed_image_name*                | Name für das Image des verwalteten Datenträgers, das erstellt wird |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Diese Vorlage erstellt ein Ubuntu 16.04 LTS-Image, installiert NGINX und hebt die Bereitstellung des virtuellen Computers auf.

> [!NOTE]
> Wenn Sie diese Vorlage mit Anmeldeinformationen des Benutzers erweitern, passen Sie den Provisioner-Befehl zum Aufheben der Bereitstellung des Azure-Agents so an, dass `-deprovision` statt `deprovision+user` gelesen wird.
> Das Flag `+user` entfernt alle Benutzerkonten aus der Quell-VM.


## <a name="build-packer-image"></a>Erstellen des Packer-Images
Wenn Packer noch nicht auf dem lokalen Computer installiert sein sollte, [befolgen Sie die Installationsanweisungen für Packer](https://www.packer.io/docs/install/index.html).

Erstellen Sie das Image, indem Sie Ihre Packer-Vorlagendatei wie folgt angeben:

```bash
./packer build ubuntu.json
```

Ein Beispiel der Ausgabe der vorherigen Befehle lautet wie folgt:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```


## <a name="create-vm-from-azure-image"></a>Erstellen einer VM anhand des Azure-Images
Sie können nun mit [az vm create](/cli/azure/vm#create) eine VM anhand Ihres Images erstellen. Geben Sie mit dem Parameter `--image` das Image an, das Sie erstellt haben. Im folgenden Beispiel wird eine VM namens *myVM* anhand von *myPackerImage* erstellt, und es werden SSH-Schlüssel generiert, sofern noch nicht vorhanden:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Das Erstellen der VM dauert einige Minuten. Nachdem die VM erstellt wurde, notieren Sie den Wert von `publicIpAddress`, der von der Azure CLI angezeigt wird. Diese Adresse wird verwendet, um über einen Webbrowser auf die NGINX-Website zuzugreifen.

Damit Webdatenverkehr Ihren virtuellen Computer erreicht, öffnen Sie Port 80 über das Internet mit [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testen der VM und von NGINX
Jetzt können Sie einen Webbrowser öffnen und `http://publicIpAddress` in die Adressleiste eingeben. Geben Sie Ihre eigene öffentliche IP-Adresse aus dem Erstellungsprozess des virtuellen Computers an. Ihre NGINX-Standardseite wird wie im folgenden Beispiel angezeigt:

![NGINX-Standardwebsite](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Nächste Schritte
In diesem Beispiel haben Sie Packer zum Erstellen eines VM-Images verwendet, in dem NGINX bereits installiert ist. Sie können dieses VM-Image zusammen mit vorhandenen Bereitstellungsworkflows nutzen, um z.B. Ihre anhand des Images erstellte App mit Ansible, Chef oder Puppet auf VMs bereitzustellen.

Weitere Packer-Beispielvorlagen für andere Linux-Distributionen finden Sie in [diesem GitHub-Repository](https://github.com/hashicorp/packer/tree/master/examples/azure).