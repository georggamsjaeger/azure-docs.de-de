---
title: "Einrichten Ihrer Entwicklungsumgebung unter Mac OS X für die Verwendung mit Azure Service Fabric | Microsoft-Dokumentation"
description: "Installieren Sie Laufzeit, SDK und Tools, und erstellen Sie einen lokalen Entwicklungscluster. Nach Abschluss des Setups können Sie mit der Erstellung von Anwendungen unter Mac OS X beginnen."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 328b2778a68e32d95b666124bf7bba969a5f52a6
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Einrichten Ihrer Entwicklungsumgebung unter Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Sie können Azure Service Fabric-Anwendungen für die Ausführung in Linux-Clustern unter Mac OS X erstellen. In diesem Dokument erfahren Sie, wie Sie Ihren Mac für die Entwicklung einrichten.

## <a name="prerequisites"></a>Voraussetzungen
Azure Service Fabric wird nicht nativ unter Mac OS X ausgeführt. Für die Ausführung eines lokalen Service Fabric-Clusters wird ein vorkonfiguriertes Docker-Containerimage bereitgestellt. Bevor Sie beginnen, benötigen Sie Folgendes:

* Mindestens 4 GB RAM
* Neueste Version von [Docker](https://www.docker.com/)
* Zugriff auf ein [Docker-Containerimage vom Typ „Service Fabric One-Box“](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/).

>[!TIP]
>
>Führen Sie die Schritte in der [Docker-Dokumentation](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) aus, um Docker auf Ihrem Mac zu installieren. [Überprüfen Sie anschließend die Installation](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Erstellen eines lokalen Containers und Einrichten von Service Fabric
Führen Sie die folgenden Schritte aus, um einen lokalen Docker-Container einzurichten und darin einen Service Fabric-Cluster auszuführen:

1. Rufen Sie das Service Fabric-One-Box-Containerimage per Pull aus dem Docker Hub-Repository ab:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Aktualisieren Sie die Konfiguration des Docker-Daemons auf dem Host mit den folgenden Einstellungen, und starten Sie den Docker-Daemon neu: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Sie können diese Einstellungen direkt in der Datei „daemon.json“ im Docker-Installationspfad aktualisieren.
    
    >[!NOTE]
    >
    >Der Speicherort der Datei „daemon.json“ kann sich von Computer zu Computer unterscheiden. Beispiel: ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json
    >
    >Es wird empfohlen, die Daemon-Konfigurationseinstellungen direkt in Docker zu ändern. Klicken Sie auf das **Docker-Symbol** und anschließend auf **Einstellungen** > **Daemon** > **Erweitert**.
    >

3. Starten Sie eine Service Fabric-One-Box-Containerinstanz, und verwenden Sie das Image, das Sie im ersten Schritt abgerufen haben:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Geben Sie einen Namen für Ihre Containerinstanz an, um die Lesbarkeit zu verbessern. 
    >
    >Wenn Ihre Anwendung an bestimmten Ports lauscht, müssen diese mit zusätzlichen Tags vom Typ `-p` angegeben werden. Beispiel: Wenn Ihre Anwendung an Port 8080 lauscht, fügen Sie das folgende `-p`-Tag hinzu:
    >
    >`run docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox`
    >

4. Melden Sie sich im interaktiven SSH-Modus am Docker-Container an:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Führen Sie das Setupskript aus, um die erforderlichen Abhängigkeiten abzurufen, und starten Sie den Cluster im Container:

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Wenn Schritt 5 abgeschlossen ist, navigieren Sie auf Ihrem Mac zu `http://localhost:19080`. Service Fabric Explorer sollte angezeigt werden.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Einrichten der Service Fabric-Befehlszeilenschnittstelle (sfctl) auf Ihrem Mac

Befolgen Sie die Anweisungen unter [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md#cli-mac), um die Service Fabric-Befehlszeilenschnittstelle (`sfctl`) auf Ihrem Mac zu installieren.
Die Befehle der Befehlszeilenschnittstelle unterstützen die Interaktion mit Service Fabric-Entitäten wie Clustern, Anwendungen und Diensten.

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Erstellen einer Anwendung auf Ihrem Mac mit Yeoman

Die Gerüstbautools von Service Fabric unterstützen Sie beim Erstellen einer Service Fabric-Anwendung über das Terminal unter Verwendung des Yeoman-Vorlagengenerators. Führen Sie die folgenden Schritte aus, um sicherzustellen, dass der Service Fabric-Yeoman-Vorlagengenerator auf dem Computer verwendet werden kann:

1. Node.js und Node Package Manager (NPM) müssen auf dem Mac installiert sein. Die Software kann wie folgt mithilfe von [HomeBrew](https://brew.sh/) installiert werden:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installieren Sie den [Yeoman](http://yeoman.io/)-Vorlagengenerator auf dem Computer über NPM:

    ```bash
    npm install -g yo
    ```
3. Installieren Sie den gewünschten Yeoman-Generator gemäß der Anleitung in der [Dokumentation zu den ersten Schritten](service-fabric-get-started-linux.md). Führen Sie zum Erstellen von Service Fabric-Anwendungen mit Yeoman die folgenden Schritte aus:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Zur Erstellung einer Service Fabric-Java-Anwendung auf einem Mac müssen JDK 1.8 und Gradle auf dem Hostcomputer installiert sein. Die Software kann wie folgt mithilfe von [HomeBrew](https://brew.sh/) installiert werden: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Bereitstellen der Anwendung auf Ihrem Mac über das Terminal

Nachdem Sie Ihre Service Fabric-Anwendung erstellt und den Buildvorgang durchgeführt haben, können Sie sie mithilfe der [Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md#cli-mac) bereitstellen:

1. Stellen Sie eine Verbindung mit dem Service Fabric-Cluster her, der unter der Containerinstanz auf Ihrem Mac ausgeführt wird:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Führen Sie in Ihrem Projektverzeichnis das Installationsskript aus:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Einrichten der .NET Core 2.0-Entwicklung

Installieren Sie das [.NET Core 2.0 SDK für Mac](https://www.microsoft.com/net/core#macos), um mit dem [Erstellen von Service Fabric-C#-Anwendungen](service-fabric-create-your-first-linux-application-with-csharp.md) zu beginnen. Pakete für Service Fabric-Anwendungen mit .NET Core 2.0 werden auf NuGet.org gehostet (derzeit in der Vorschauphase).

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Installieren des Service Fabric-Plug-Ins für Eclipse Neon auf Ihrem Mac

Azure Service Fabric stellt ein Plug-In für Neon Eclipse für die Java-IDE bereit. Das Plug-In vereinfacht den Prozess der Erstellung und Bereitstellung von Java-Diensten. Führen Sie [diese Schritte](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) aus, um das Service Fabric-Plug-In für Eclipse zu installieren oder auf die aktuelle Version zu aktualisieren. Die anderen Schritte unter [Service Fabric-Plug-In für die Entwicklung von Eclipse-Java-Anwendungen](service-fabric-get-started-eclipse.md) gelten ebenfalls: Erstellen einer Anwendung, Hinzufügen eines Diensts zu einer Anwendung, Deinstallieren einer Anwendung usw.

Der letzte Schritt besteht im Instanziieren des Containers mit einem Pfad, der mit Ihrem Host gemeinsam verwendet wird. Für das Plug-In ist diese Art der Instanziierung erforderlich, damit es mit dem Docker-Container auf Ihrem Mac verwendet werden kann. Beispiel:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```

Die Attribute sind wie folgt definiert:
* `/Users/sayantan/work/workspaces/mySFWorkspace` ist der vollqualifizierte Pfad des Arbeitsbereichs auf Ihrem Mac.
* `/tmp/mySFWorkspace` ist der Pfad innerhalb des Containers, dem der Arbeitsbereich zugeordnet werden soll.

>[!NOTE]
> 
>Wenn Ihr Name/Pfad für den Arbeitsbereich anders lautet, aktualisieren Sie diese Werte im Befehl `docker run`.
> 
>Falls Sie den Container mit einem anderen Namen als `sfonebox` starten, sollten Sie den Wert des Namens in der Datei „testclient.sh“ in Ihrer Service Fabric Actor-Java-Anwendung aktualisieren.
>

## <a name="next-steps"></a>Nächste Schritte
<!-- Links -->
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe von Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe des Service Fabric-Plug-Ins für Eclipse](service-fabric-get-started-eclipse.md)
* [Erstellen eines Service Fabric-Clusters in Azure über das Azure-Portal](service-fabric-cluster-creation-via-portal.md)
* [Erstellen eines Service Fabric-Clusters in Azure mithilfe von Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Modellieren von Anwendungen in Service Fabric](service-fabric-application-model.md)
* [Verwalten von Anwendungen mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md)
* [Vorbereiten einer Linux-Entwicklungsumgebung unter Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
