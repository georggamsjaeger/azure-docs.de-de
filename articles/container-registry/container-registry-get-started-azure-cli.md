---
title: 'Schnellstartanleitung: Erstellen einer privaten Docker-Registrierung in Azure mit der Azure CLI'
description: Hier lernen Sie, wie Sie schnell eine private Docker-Containerregistrierung mit der Azure CLI erstellen.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 5cddf0ffea256ed6d1c51d48a61ac8176d08b9cc
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Erstellen einer Containerregistrierung mit der Azure-Befehlszeilenschnittstelle

Azure Container Registry ist ein verwalteter Docker-Containerregistrierungsdienst zum Speichern privater Docker-Containerimages. In diesem Leitfaden erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine Azure Container Registry-Instanz erstellen.

Für diese Schnellstartanleitung müssen Sie mindestens Version 2.0.20 der Azure-Befehlszeilenschnittstelle ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli) Informationen dazu.

Darüber hinaus muss Docker lokal installiert sein. Für Docker sind Pakete erhältlich, mit denen Docker problemlos auf einem [Mac](https://docs.docker.com/docker-for-mac/)-, [Windows](https://docs.docker.com/docker-for-windows/)- oder [Linux](https://docs.docker.com/engine/installation/#supported-platforms)-System konfiguriert werden kann.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

In dieser Schnellstartanleitung erstellen wir eine *Basic*-Registrierung. Azure Container Registry steht in mehreren unterschiedlichen SKUs zur Verfügung, die in der folgenden Tabelle kurz beschrieben werden. Ausführliche Details zu den einzelnen Einträgen finden Sie unter [Containerregistrierungs-SKUs](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Erstellen Sie mithilfe des Befehls [az acr create](/cli/azure/acr#create) eine ACR-Instanz.

Der Name der Registrierung **muss eindeutig sein**. Im folgenden Beispiel wird der Name *myContainerRegistry007* verwendet. Ersetzen Sie diesen Namen durch einen eindeutigen Wert.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

Wenn die Registrierung erstellt wird, sieht die Ausgabe etwa wie folgt aus:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Für den Rest dieser Schnellstartanleitung verwenden wir `<acrname>` als Platzhalter für den Namen der Containerregistrierung.

## <a name="log-in-to-acr"></a>Anmelden bei ACR

Bevor Sie Push- und Pullvorgänge für Containerimages ausführen können, müssen Sie sich bei der ACR-Instanz anmelden. Verwenden Sie hierzu den Befehl [az acr login](/cli/azure/acr#login).

```azurecli
az acr login --name <acrname>
```

Nach Abschluss des Vorgangs wird eine Erfolgsmeldung zurückgegeben.

## <a name="push-image-to-acr"></a>Übertragen eines Images an ACR mithilfe von Push

Um ein Image mithilfe von Push an Ihre Azure Container Registry-Instanz übertragen zu können, benötigen Sie zunächst ein Image. Führen Sie bei Bedarf den folgenden Befehl aus, um ein vorab erstelltes Image von Docker Hub abzurufen:

```bash
docker pull microsoft/aci-helloworld
```

Das Image muss mit dem ACR-Anmeldeservernamen gekennzeichnet sein. Führen Sie den folgenden Befehl aus, um den Anmeldeservernamen der ACR-Instanz zurückzugeben.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Markieren Sie das Image mithilfe des Befehls [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Ersetzen Sie *<acrLoginServer>* durch den Anmeldeservernamen Ihrer ACR-Instanz.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Nun können Sie das Image mithilfe von [docker push](https://docs.docker.com/engine/reference/commandline/push/) an die ACR-Instanz übertragen. Ersetzen Sie *<acrLoginServer>* durch den Anmeldeservernamen Ihrer ACR-Instanz.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Auflisten von Containerimages

Das folgende Beispiel listet die Repositorys in einer Registrierung auf:

```azurecli
az acr repository list -n <acrname> -o table
```

Ausgabe:

```bash
Result
----------------
aci-helloworld
```

Das folgende Beispiel listet die Tags des Repositorys **aci-helloworld** auf:

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Ausgabe:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, die ACR-Instanz und alle Containerimages nicht mehr benötigen, können Sie sie mit dem Befehl [az group delete](/cli/azure/group#delete) entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Azure Container Registry-Instanz über die Azure-Befehlszeilenschnittstelle erstellt. Wenn Sie Azure Container Registry mit Azure Container Instances verwenden möchten, fahren Sie mit dem Tutorial zu Azure Container Instances fort.

> [!div class="nextstepaction"]
> [Azure Container Instances-Tutorial](../container-instances/container-instances-tutorial-prepare-app.md)