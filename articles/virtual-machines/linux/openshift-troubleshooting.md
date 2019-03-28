---
title: Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: c65e76fb9453e93e856c76f397d187f9ee740fbd
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540350"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure

Jeśli klaster OpenShift nie wdrożony pomyślnie, witryny Azure portal zapewnia dane wyjściowe błędu. Dane wyjściowe może być trudne do odczytania, co czyni go trudno zidentyfikować problem. Szybko przeskanować te dane wyjściowe dla kodu zakończenia 3, 4 lub 5. Poniżej znajdują się informacje na te kody zakończenia trzy:

- Kod zakończenia 3: Nazwa Red Hat subskrypcji użytkownika / hasło lub identyfikator organizacji / klucz aktywacji jest nieprawidłowy
- Kod zakończenia 4: Identyfikator puli Red Hat są niepoprawne lub uprawnienia nie są dostępne
- Kod zakończenia 5: Nie można aprowizować rozmiar woluminu puli elastycznej platformy Docker

Wszystkie pozostałe kody zakończenia można znaleźć hostami za pośrednictwem protokołu ssh do wyświetlania plików dziennika.

**OpenShift Container Platform**

Protokół SSH z hostem element playbook rozwiązania ansible. Szablon lub oferty w portalu Marketplace należy użyć hostem bastionu. Z bastionu możesz SSH do wszystkich innych węzłów w klastrze (wzorca, infra, CNS, składnik obliczeniowy). Musisz być użytkownikiem głównym, aby wyświetlić pliki dziennika. Główny jest dostępu SSH domyślnie wyłączona, dlatego nie należy używać głównego SSH do innych węzłów.

**OKD**

Protokół SSH z hostem element playbook rozwiązania ansible. W przypadku OKD szablonu (w wersji 3.9 i starszych) Użyj hosta master-0. W przypadku OKD szablonu (w wersji 3.10 i nowsze) Użyj hostem bastionu. Z hosta element playbook rozwiązania ansible możesz SSH do wszystkich innych węzłów w klastrze (wzorca, infra, CNS, składnik obliczeniowy). Musisz być użytkownikiem głównym ("sudo" su-), aby wyświetlić pliki dziennika. Główny jest dostępu SSH domyślnie wyłączona, dlatego nie należy używać głównego SSH do innych węzłów.

## <a name="log-files"></a>Pliki dziennika

Pliki dziennika (stderr i stdout) dla hosta skryptów przygotowania znajdują się w /var/lib/waagent/custom-script/download/0 na wszystkich hostach. Jeśli wystąpił błąd podczas przygotowywania hosta, wyświetlać te pliki dziennika, aby określić błędu.

Jeśli skrypty przygotowania został uruchomiony pomyślnie, następnie pliki dziennika w katalogu /var/lib/waagent/custom-script/download/1 hosta element playbook rozwiązania ansible należy do badania. Jeśli błąd wystąpił podczas instalacji rzeczywistego OpenShift, plik stdout wyświetli błąd. Dzięki tym informacjom można się z pomocą techniczną, aby uzyskać dalszą pomoc.

Przykładowe dane wyjściowe

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Najbardziej typowe błędy podczas instalacji są:

1. Klucz prywatny ma hasło
2. Klucz tajny usługi Key vault, przy użyciu klucza prywatnego nie został utworzony prawidłowo
3. Wprowadzono nieprawidłowe poświadczenia nazwy głównej usługi
4. Nazwa główna usługi nie ma prawa dostępu współautora do grupy zasobów

### <a name="private-key-has-a-passphrase"></a>Klucz prywatny jest hasło

Zostanie wyświetlony błąd, który nie przyznano uprawnienia dla protokołu SSH. Protokół SSH z hosta element playbook rozwiązania ansible pod kątem hasło klucza prywatnego.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Klucz tajny usługi Key vault, przy użyciu klucza prywatnego nie został utworzony prawidłowo

Klucz prywatny są wstrzykiwane do hosta element playbook rozwiązania ansible - ~/.ssh/id_rsa. Upewnij się, że ten plik jest poprawny. Testowanie, otwierając sesję SSH z jednym z węzłów klastra z hosta element playbook rozwiązania ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Wprowadzono nieprawidłowe poświadczenia nazwy głównej usługi

Podczas dostarczania wprowadzanie do szablonu lub oferty w portalu Marketplace, podano nieprawidłowe informacje. Upewnij się, że używasz poprawny identyfikator aplikacji (clientId) oraz hasło (clientSecret) dla jednostki usługi. Sprawdź, wydając polecenie wiersza polecenia platformy azure.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Nazwa główna usługi nie ma prawa dostępu współautora do grupy zasobów

Jeśli jest włączony dostawca chmury platformy Azure, jednostki usługi używany musi mieć prawa dostępu współautora do grupy zasobów. Sprawdź, wydając polecenie wiersza polecenia platformy azure.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Dodatkowe narzędzia

Niektóre błędy umożliwia także następujące polecenia, aby uzyskać więcej informacji:

1. systemctl status \<service>
2. journalctl -xe
