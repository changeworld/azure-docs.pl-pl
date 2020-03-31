---
title: Rozwiązywanie problemów z bastionem platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak rozwiązywać problemy z bastionem platformy Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512953"
---
# <a name="troubleshoot-azure-bastion"></a>Rozwiązywanie problemów z bastionem platformy Azure

W tym artykule pokazano, jak rozwiązywać problemy z bastionem platformy Azure.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Nie można utworzyć sieciowej bazy sieciowej w witrynie AzureBastionSubnet

**P.** Podczas próby utworzenia sieciowej grupy zabezpieczeń w podsieci Bastion platformy Azure pojawia się następujący błąd: * <NSG name> "Grupa zabezpieczeń sieci nie ma niezbędnych reguł dla podsieci Azure Bastion AzureBastionSubnet"*.

**Odp.:** Jeśli utworzysz i zastosujesz sieć sieciową do *usługi AzureBastionSubnet,* upewnij się, że dodano następujące reguły w sieciowej sieciowej. Jeśli te reguły nie zostaną dodane, tworzenie/aktualizowanie sieciowej sieciowej zakończy się niepowodzeniem.

1. Łączność płaszczyzny sterowania — przychodzące na 443 z GatewayManager
2. Rejestrowanie diagnostyki i inne — wychodzące na 443 do azurecloud (tagi regionalne w tym tagu usługi nie są jeszcze obsługiwane).
3. Maszyna wirtualna docelowa — wychodzące dla sieci wirtualnej 3389 i 22

Przykład reguł sieciowej grupy danych sieciowych jest dostępny w szablonie [szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące nsg dla usługi Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Nie można użyć klucza SSH w bastionie platformy Azure

**P.** Podczas próby przeglądania pliku klucza SSH pojawia się następujący błąd: *'Klucz prywatny SSH musi zaczynać się od -----BEGIN RSA PRIVATE KEY----- a kończy się -----END RSA PRIVATE KEY-----'*.

**Odp.:** Usługa Azure Bastion obsługuje tylko klucze SSH RSA, w tym momencie. Upewnij się, że przeglądasz plik klucza, który jest kluczem prywatnym RSA dla SSH, z kluczem publicznym aprowizacji na docelowej maszynie wirtualnej. 

Na przykład można użyć następującego polecenia, aby utworzyć nowy klucz RSA SSH:

**ssh-keygen -t rsa -b 4096 -C " "email@domain.com**

Dane wyjściowe:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Nie można zalogować się do maszyny wirtualnej przyłączanych do domeny systemu Windows

**P.** Nie mogę połączyć się z maszyną wirtualną systemu Windows, która jest przyłączona do domeny.

**Odp.:** Usługa Azure Bastion obsługuje logowanie się do maszyny Wirtualnej przyłączone do domeny tylko w przypadku logowania się do domeny opartej na hasłach użytkownika. Podczas określania poświadczeń domeny w witrynieusername@domainAzure portal należy użyć formatu NAZWY UPN ( ) zamiast formatu *domeny\nazwa_użytkownika,* aby się zalogować. Jest to obsługiwane dla maszyn wirtualnych przyłączonych do domeny lub przyłączonych hybrydowo (zarówno przyłączonych do domeny, jak i przyłączonych do usługi Azure AD). Nie jest obsługiwana dla maszyn wirtualnych przyłączonych tylko do usługi Azure AD.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemy z transferem plików

**P.** Czy transfer plików jest obsługiwany przez usługę Azure Bastion?

**Odp.:** Przesyłanie plików nie jest obecnie obsługiwane. Pracujemy nad dodaniem wsparcia.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Czarny ekran w witrynie Azure portal

**P.** Gdy próbuję połączyć się przy użyciu usługi Azure Bastion, otrzymuję czarny ekran w witrynie Azure portal.

**Odp.:** Dzieje się tak, gdy występuje problem z łącznością sieciową między przeglądarką sieci web a kontem Azure Bastion (zapora internetowa klienta może blokować ruch WebSockets lub podobny) lub między bastionem platformy Azure a docelową maszyną wirtualną. Większość przypadków obejmują sieciowej grupy sieciowej stosowanej do usługi AzureBastionSubnet lub w docelowej podsieci maszyny Wirtualnej, która blokuje ruch RDP/SSH w sieci wirtualnej. Zezwalaj na ruch WebSockets na zaporze internetowej klienta i sprawdź sieciowe grupy zabezpieczeń w docelowej podsieci maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz często zadawane [pytania dotyczące bastionu](bastion-faq.md).