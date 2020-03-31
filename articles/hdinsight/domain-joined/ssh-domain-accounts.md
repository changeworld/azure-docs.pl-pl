---
title: Zarządzanie dostępem SSH dla kont domeny w usłudze Azure HDInsight
description: Kroki zarządzania dostępem SSH dla kont usługi Azure AD w programie HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472520"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Zarządzanie dostępem SSH dla kont domeny w usłudze Azure HDInsight

W bezpiecznych klastrach domyślnie wszyscy użytkownicy domeny w [usługach Azure AD DS](../../active-directory-domain-services/overview.md) mogą [ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) do węzłów head i edge. Ci użytkownicy nie są częścią grupy sudoers i nie otrzymują dostępu do roota. Użytkownik SSH utworzony podczas tworzenia klastra będzie miał dostęp do roota.

## <a name="manage-access"></a>Zarządzanie dostępem

Aby zmodyfikować dostęp SSH do `/etc/ssh/sshd_config` określonych użytkowników lub grup, zaktualizuj każdy z węzłów.

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Otwórz `ssh_confi`plik g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Zmodyfikuj plik zgodnie z `sshd_config` potrzebami. Jeśli ograniczysz użytkowników do niektórych grup, konta lokalne nie mogą ssh do tego węzła. Oto tylko przykład składni:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Następnie zapisz zmiany: **Ctrl + X**, **Y**, **Enter**.

1. Uruchom ponownie sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Powtórz powyższe kroki dla każdego węzła.

## <a name="ssh-authentication-log"></a>Dziennik uwierzytelniania SSH

Dziennik uwierzytelniania SSH `/var/log/auth.log`jest zapisywany w pliku . Jeśli widzisz żadnych błędów logowania za pośrednictwem SSH dla kont lokalnych lub domeny, należy przejść przez dziennik do debugowania błędów. Często problem może być związany z określonymi kontami użytkowników i zazwyczaj dobrą praktyką jest wypróbowanie innych kont użytkowników lub SSH przy użyciu domyślnego użytkownika SSH (konta lokalnego), a następnie próba kinit.

## <a name="ssh-debug-log"></a>Dziennik debugowania SSH

Aby włączyć pełne rejestrowanie, należy ponownie `sshd` uruchomić `-d` z opcją. Podobnie `/usr/sbin/sshd -d` jak Ty `sshd` można również uruchomić w porcie niestandardowym (jak 2222), dzięki czemu nie trzeba zatrzymać głównego demona SSH. Można również `-v` użyć opcji z klientem SSH, aby uzyskać więcej dzienników (widok po stronie klienta błędów).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami HDInsight za pomocą pakietu zabezpieczeń przedsiębiorstwa](./apache-domain-joined-manage.md)
* [Połącz się z HDInsight (Apache Hadoop) za pomocą SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
