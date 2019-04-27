---
title: Certyfikowanie obrazu maszyny Wirtualnej w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Opisano sposób testowania i przesłać obraz maszyny Wirtualnej do certyfikacji w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 24430b1b785a24da06a8ea51594147040e6d5bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638393"
---
# <a name="certify-your-vm-image"></a>Certyfikowanie obrazu maszyny Wirtualnej

Po utworzeniu i wdrażanie maszyny wirtualnej (VM) należy przetestować i przesłać obraz maszyny Wirtualnej do certyfikacji w portalu Azure Marketplace. W tym artykule opisano, gdzie można uzyskać *narzędzie Test certyfikacji do certyfikatu platformy Azure*, jak certyfikować swój obraz maszyny Wirtualnej za pomocą tego narzędzia i jak można przekazać wyników weryfikacji do kontenera platformy Azure, w którym znajdują się Twoje wirtualne dyski twarde. 


## <a name="download-and-run-the-certification-test-tool"></a>Pobierz i uruchom narzędzie test certyfikacji

Narzędzie Test certyfikacji do certyfikatu platformy Azure działa na komputerze lokalnym, Windows, ale testów opartych na platformie Azure Windows lub maszyny Wirtualnej systemu Linux.  Sprawdza, czy obrazu użytkownika maszyny Wirtualnej jest zgodna z platformą Microsoft Azure — czy zostały spełnione wytycznych oraz wymagań dotyczących przygotowywania wirtualnego dysku twardego. Wynikiem pracy narzędzia jest raport zgodności, który możesz przekazać do [portalu Cloud Partner](https://cloudpartner.azure.com) żądanie certyfikacji maszyny Wirtualnej.

1. Pobieranie i instalowanie najnowszych, ostatnio [narzędzie Test certyfikacji do certyfikatu platformy Azure](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Otwórz narzędzie certyfikacji, a następnie kliknij przycisk **Rozpocznij nowy Test**.
3. Z **informacji dotyczących testu** ekranu, należy wprowadzić **nazwa testu** dla przebiegu testu.
4. Wybierz **platformy** dla maszyny Wirtualnej, albo `Windows Server` lub `Linux`. Wybór platformy ma wpływ na pozostałe opcje.
5. Jeśli maszyna wirtualna korzysta z tej bazy danych usługi, wybierz **testu dla usługi Azure SQL Database** pola wyboru.

   ![Strona początkowa narzędzie test certyfikatu](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Połącz narzędzie certyfikacji obrazem maszyny Wirtualnej

  Narzędzie łączy się maszyny wirtualne oparte na Windows za pomocą [PowerShell](https://docs.microsoft.com/powershell/) i nawiązanie połączenia z maszyn wirtualnych systemu Linux przy użyciu [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Połącz narzędzie certyfikacji obrazu maszyny Wirtualnej systemu Linux

1. Wybierz **uwierzytelniania SSH** tryb: `Password Authentication` lub `key File Authentication`.
2. W przypadku wybrania uwierzytelniania opartego na hasłach, wprowadź wartości dla **nazwę DNS maszyny Wirtualnej**, **nazwa_użytkownika**, i **hasło**.  Opcjonalnie możesz zmienić domyślną **portu SSH** numer.

     ![Uwierzytelnianie hasłem obrazu maszyny Wirtualnej systemu Linux](./media/publishvm_026.png)

3. W przypadku wybrania uwierzytelniania opartego na pliku klucza wprowadź wartości dla **nazwę DNS maszyny Wirtualnej**, **nazwa_użytkownika**, i **klucza prywatnego** lokalizacji.  Opcjonalnie możesz podać **hasło** bądź zmienić domyślny typ **portu SSH** numer.

     ![Uwierzytelniania opartego na pliku obrazu maszyny Wirtualnej systemu Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Połącz narzędzie certyfikacji obrazem maszyny Wirtualnej z systemem Windows**
1. Wprowadź w pełni kwalifikowaną **nazwę DNS maszyny Wirtualnej** (na przykład `MyVMName.Cloudapp.net`).
2. Wprowadź wartości w polach **nazwa_użytkownika** i **hasło**.

   ![Hasło uwierzytelniania Windows obrazu maszyny Wirtualnej](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Uruchom test certyfikacji

Po wartości parametrów wprowadzonych w narzędziu certyfikacji obrazu maszyny Wirtualnej, wybierz **Testuj połączenie** aby zapewnić prawidłowe połączenie z maszyną wirtualną. Po zweryfikowaniu połączenia kliknij pozycję Wybierz **dalej** aby rozpocząć test.  Po zakończeniu testu tabeli jest wyświetlany z wynikami testu (— dostęp próbny/niepowodzenie/ostrzeżenie).  Poniższy przykład pokazuje wyniki testów dla testów maszyny Wirtualnej systemu Linux. 

![Wyniki testu certyfikacji obrazu maszyny Wirtualnej systemu Linux](./media/publishvm_029.png)

Jeśli którykolwiek z testów nie powiedzie się, obraz jest *nie* certyfikat. W tym przypadku Przegląd wymagań i komunikaty o błędach, wskazano zmiany i ponownie uruchom test. 

Po testów automatycznych, należy podać dodatkowe informacje o obrazie maszyny Wirtualnej na **kwestionariusz** ekranu.  Zawiera ona dwie karty, które należy wykonać.  **Ogólną ocenę** karta zawiera **PRAWDA/FAŁSZ** pytania, natomiast **dostosowywania jądra** zawiera wiele oraz dowolne pytania.  Wykonaj pytania na obu kartach, a następnie wybierz pozycję **dalej**.

![Kwestionariusz narzędzie certyfikacji](./media/publishvm_030.png)

Ostatnim ekranie umożliwia podanie dodatkowych informacji, takich jak SSH dostęp do informacji dla obrazu maszyny Wirtualnej systemu Linux i wyjaśnienia dla dowolnego oceny nie powiodło się, jeśli szukają wyjątki. 

Na koniec kliknij **Generowanie raportu** pobrać wyniki testów i pliki dziennika dla wykonanych testów dodatkowo do odpowiedzi na kwestionariusz. Zapisz wyniki w tym samym kontenerze co z wirtualnymi dyskami twardymi.

![Zapisz certyfikacji wyników testu](./media/publishvm_031.png)


## <a name="next-steps"></a>Kolejne kroki

Następnie zostanie [Generowanie identyfikatory uniform resource identifier (URI) dla poszczególnych wirtualnych dysków Twardych](./cpp-get-sas-uri.md) przesyłanego do portalu marketplace. 
