---
title: "Schnellstartanleitung: Azure Docker CE-Cluster für Linux"
description: "Hier erfahren Sie, wie Sie in Azure Container Service mithilfe der Azure-Befehlszeilenschnittstelle einen Docker CE-Cluster für Linux-Container erstellen."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 67cea1bb11ac8b3ad05676eaba875da7d4726b6d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-docker-ce-cluster"></a>Bereitstellen eines Docker CE-Clusters

In dieser Schnellstartanleitung wird ein Docker CE-Cluster mithilfe der Azure-Befehlszeilenschnittstelle bereitgestellt. Anschließend wird in dem Cluster eine Anwendung mit mehreren Containern bereitgestellt und ausgeführt, die ein Web-Front-End und eine Redis-Instanz umfasst. Nach Abschluss des Vorgangs kann auf die Anwendung über das Internet zugegriffen werden.

Docker CE in Azure Container Service befindet sich in der Vorschauphase und **darf nicht für Produktionsworkloads verwendet werden**.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli) Informationen dazu.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe namens *myResourceGroup* am Standort *ukwest* erstellt.

```azurecli-interactive
az group create --name myResourceGroup --location ukwest
```

Ausgabe:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "ukwest",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Erstellen eines Docker Swarm-Clusters

Erstellen Sie mit dem Befehl [az acs create](/cli/azure/acs#create) einen Docker CE-Cluster in Azure Container Service. 

Im folgenden Beispiel wird ein Cluster namens *mySwarmCluster* mit einem Linux-Masterknoten und drei Linux-Agent-Knoten erstellt.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

Manchmal hat ein Azure-Abonnement eingeschränkten Zugriff auf Azure-Ressourcen. Dies ist beispielsweise bei einem eingeschränkten Testabonnement der Fall. Tritt bei der Bereitstellung ein Fehler aufgrund von begrenzt verfügbaren Kernen auf, verringern Sie die Anzahl der Standard-Agents, indem Sie `--agent-count 1` zum Befehl [az acs create](/cli/azure/acs#create) hinzufügen. 

Nach einigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster im JSON-Format zurückgegeben.

## <a name="connect-to-the-cluster"></a>Verbinden mit dem Cluster

In dieser Schnellstartanleitung benötigen Sie den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) des Docker Swarm-Masters und des Docker-Agent-Pools. Führen Sie den folgenden Befehl aus, um die FQDNs für Master und Agent zurückgegeben.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Ausgabe:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Erstellen Sie einen SSH-Tunnel zum Swarm-Master. Ersetzen Sie `MasterFQDN` durch die FQDN-Adresse des Swarm-Masters.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Legen Sie die Umgebungsvariable `DOCKER_HOST` fest. Dadurch können Sie Docker-Befehle für Docker Swarm ausführen, ohne den Namen des Hosts anzugeben.

```bash
export DOCKER_HOST=localhost:2374
```

Nun können Sie Docker-Dienste für Docker Swarm ausführen.


## <a name="run-the-application"></a>Ausführen der Anwendung

Erstellen Sie eine Datei namens `azure-vote.yaml`, und fügen Sie folgenden Inhalt ein.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Führen Sie den Befehl [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) aus, um den Azure Vote-Dienst zu erstellen.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Ausgabe:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Geben Sie mithilfe des Befehls [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) den Bereitstellungsstatus der Anwendung zurück.

```bash
docker stack ps azure-vote
```

Sobald `CURRENT STATE` für jeden Dienst `Running` lautet, ist die Anwendung bereit.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:redis-v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testen der Anwendung

Navigieren Sie zum FQDN des Swarm-Agent-Pools, um die Azure Vote-Anwendung zu testen.

![Abbildung der Navigation zu Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Löschen von Clustern
Wenn der Cluster nicht mehr benötigt wird, können Sie mit dem Befehl [az group delete](/cli/azure/group#delete) die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen entfernen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Abrufen des Codes

In dieser Schnellstartanleitung wurden vorab erstellte Containerimages verwendet, um einen Docker-Dienst zu erstellen. Der dazugehörige Anwendungscode, die Dockerfile-Datei und die Compose-Datei sind auf GitHub verfügbar.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Docker Swarm-Cluster und eine Anwendung mit mehreren Containern dafür bereitgestellt.

Informationen zur Integration von Docker Swarm in Visual Studio Team Services finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [CD/CI mit Docker Swarm und VSTS](./container-service-docker-swarm-setup-ci-cd.md)
