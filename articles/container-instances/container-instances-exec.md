---
title: Wykonywanie poleceń w uruchomionych kontenerów w usłudze Azure Container Instances
description: Dowiedz się, jak wykonywać polecenia w kontenerze, który jest obecnie uruchomiony w usłudze Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60579750"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Wykonanie polecenia w wystąpieniu działa usługa Azure container

Usługa Azure Container Instances obsługuje wykonywanie polecenia w uruchomionego kontenera. Uruchomienie polecenia w kontenerze, który został już uruchomiony jest szczególnie przydatne podczas tworzenia aplikacji i rozwiązywania problemów. Najczęściej używane ta funkcja jest uruchomienie interaktywnej powłoki, aby można było debugować problemy z uruchomionego kontenera.

## <a name="run-a-command-with-azure-cli"></a>Uruchom polecenie z wiersza polecenia platformy Azure

Wykonanie polecenia w kontenerze uruchomiona przy użyciu [az container exec] [ az-container-exec] w [wiersza polecenia platformy Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Na przykład można uruchomić z poziomu powłoki Bash w kontenera Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

W przykładzie danych wyjściowych poniżej, powłoki Bash, jest uruchamiana w działający kontener systemu Linux, zapewniając terminalu, w którym `ls` jest wykonywana:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

W tym przykładzie zostanie uruchomiony wiersz polecenia w kontenerze uruchomiona Nanoserver:

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

Jeśli Twoje [grupy kontenerów](container-instances-container-groups.md) ma wiele kontenerów, takich jak kontener aplikacji i przyczepki rejestrowania, określ nazwę kontenera, w którym należy uruchomić polecenie z `--container-name`.

Na przykład, w grupie kontenerów *mynginx* są dwa kontenery *aplikacji nginx* i *rejestratora*. Można uruchomić z poziomu powłoki na *aplikacji nginx* kontenera:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Ograniczenia

Usługa Azure Container Instances obecnie obsługuje uruchamianie w pojedynczym procesie [az container exec][az-container-exec], i nie można przekazać argumenty wiersza polecenia. Na przykład nie można utworzyć łańcuch poleceń, takich jak w `sh -c "echo FOO && echo BAR"`, albo wykonaj `echo FOO`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o innych narzędzi do rozwiązywania problemów i typowych problemów z wdrażaniem w [rozwiązywać problemy kontenerów i wdrażanie w usłudze Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure