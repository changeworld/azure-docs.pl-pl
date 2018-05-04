---
title: Polecenia można wykonywać w programie kontenery wystąpień kontenera platformy Azure
description: Dowiedz się, jak wykonać polecenia w kontenerze, który jest obecnie uruchomiony w wystąpień kontenera platformy Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.openlocfilehash: 43211f620efb16cbcd722d3d386b1bb862fc6280
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Wykonanie polecenia w wystąpieniu kontener uruchamianie Azure

Wystąpień kontenera platformy Azure obsługuje wykonywania polecenia w kontenerze uruchomione. Polecenia w kontenerze, który został już uruchomiony jest szczególnie przydatne podczas tworzenia aplikacji i rozwiązywania problemów. Najbardziej typowe używanie tej funkcji jest uruchamianie interaktywnego powłoki tak, aby umożliwić debugowanie problemów w kontenerze uruchomione.

## <a name="run-a-command-with-azure-cli"></a>Uruchom polecenie z wiersza polecenia platformy Azure

Wykonania polecenia w kontenerze uruchomionych z [exec kontenera az] [ az-container-exec] w [interfejsu wiersza polecenia Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Na przykład, aby uruchomić powłoki Bash w kontenerze Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

W przykładzie danych wyjściowych poniżej, powłoki Bash jest uruchamiana w kontenerze systemu Linux uruchomionych, zapewniając terminalu, w którym `ls` jest wykonywana:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

W tym przykładzie wiersza polecenia jest uruchamiana w kontenerze Nanoserver uruchomiona:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Grupy z wieloma kontenerami

Jeśli Twoje [grupy kontenerów](container-instances-container-groups.md) ma wiele kontenerów, takie jak kontenera aplikacji i boczną rejestrowania, określ nazwę kontenera, w którym można uruchomić polecenie z `--container-name`.

Na przykład w grupie kontenera *mynginx* są dwa kontenery *aplikacji nginx* i *rejestratora*. Do uruchomienia powłoki w *aplikacji nginx* kontenera:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Ograniczenia

Wystąpień kontenera Azure obecnie obsługuje uruchamianie jednego procesu z [exec kontenera az][az-container-exec], i nie można przekazywać argumenty polecenia. Nie można na przykład łańcucha poleceń, takich jak w `sh -c "echo FOO && echo BAR"`, lub wykonaj `echo FOO`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o innych narzędzi do rozwiązywania problemów i typowe problemy z wdrażaniem w [Rozwiązywanie problemów i wdrożenia kontenera w wystąpień kontenera Azure](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure