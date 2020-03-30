---
title: Certyfikuj obraz maszyny Wirtualnej dla portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak przetestować i przesłać obraz maszyny Wirtualnej do certyfikacji usługi Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 0225069179e0d7d94a983c5161976a5c1933fac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278199"
---
# <a name="certify-your-vm-image"></a>Poświadczanie obrazu maszyny Wirtualnej

Po utworzeniu i wdrożeniu maszyny wirtualnej (VM) należy przetestować i przesłać obraz maszyny wirtualnej do certyfikacji usługi Azure Marketplace. W tym artykule wyjaśniono, gdzie można uzyskać *narzędzie testu certyfikacji dla certyfikatu platformy Azure,* jak użyć tego narzędzia do certyfikacji obrazu maszyny Wirtualnej i jak przekazać wyniki weryfikacji do kontenera platformy Azure, w którym znajdują się wirtualne identyfikatory. 


## <a name="download-and-run-the-certification-test-tool"></a>Pobieranie i uruchamianie narzędzia do testowania certyfikacji

Narzędzie testowe certyfikacji dla certyfikatu platformy Azure działa na lokalnym komputerze z systemem Windows, ale testuje maszynę wirtualną z systemem Windows lub Linux opartą na platformie Azure.  Sprawdza, czy obraz maszyny Wirtualnej użytkownika jest zgodny z platformą Microsoft Azure — że wskazówki i wymagania dotyczące przygotowywania dysku wirtualnego zostały spełnione. Dane wyjściowe narzędzia to raport zgodności, który zostanie przekazany do [portalu cloud partner w](https://cloudpartner.azure.com) celu zażądania certyfikacji maszyny wirtualnej.

1. Pobierz i zainstaluj najnowsze [narzędzie do testowania certyfikacji dla platformy Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Otwórz narzędzie certyfikacji, a następnie kliknij przycisk **Rozpocznij nowy test**.
3. Na ekranie **Informacje o teście** wprowadź **nazwę testu** dla przebiegu testu.
4. Wybierz **platformę** dla maszyny Wirtualnej, albo `Windows Server` . `Linux` Wybór platformy ma wpływ na pozostałe opcje.
5. Jeśli maszyna wirtualna korzysta z tej usługi bazy danych, zaznacz pole wyboru **Test dla usługi Azure SQL Database.**

   ![Strona początkowa narzędzia testowego certyfikatu](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Podłączanie narzędzia certyfikacji do obrazu maszyny Wirtualnej

  Narzędzie łączy się z maszynami wirtualnymi z systemem Windows za pomocą [programu PowerShell](https://docs.microsoft.com/powershell/) i łączy się z maszynami wirtualnymi z systemem Linux za pośrednictwem [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Podłączanie narzędzia certyfikacji do obrazu maszyny Wirtualnej z systemem Linux

1. Wybierz tryb **uwierzytelniania SSH:** `Password Authentication` lub `key File Authentication`.
2. W przypadku korzystania z uwierzytelniania opartego na hasłach wprowadź wartości **nazwy DNS maszyny Wirtualnej,** **nazwy użytkownika**i **hasła**.  Opcjonalnie można zmienić domyślny numer **portu SSH.**

     ![Uwierzytelnianie hasłem obrazu maszyny wirtualnej systemu Linux](./media/publishvm_026.png)

3. W przypadku korzystania z uwierzytelniania opartego na plikach kluczy wprowadź wartości **dla nazwy DNS maszyny Wirtualnej,** nazwy **użytkownika**i lokalizacji **klucza prywatnego.**  Opcjonalnie można podać hasło lub zmienić **domyślny** numer **portu SSH.**

     ![Uwierzytelnianie plików obrazu maszyny wirtualnej systemu Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Łączenie narzędzia certyfikacji z obrazem maszyny wirtualnej z systemem Windows**
1. Wprowadź w pełni kwalifikowaną nazwę DNS `MyVMName.Cloudapp.net`maszyny **Wirtualnej** (na przykład ).
2. Wprowadź wartości **nazwy użytkownika** i **hasła**.

   ![Uwierzytelnianie hasłem obrazu maszyny Wirtualnej systemu Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Uruchamianie testu certyfikacyjnego

Po podasz wartości parametrów obrazu maszyny Wirtualnej w narzędziu certyfikacji wybierz **opcję Testuj połączenie,** aby zapewnić prawidłowe połączenie z maszyną wirtualną. Po zweryfikowaniu połączenia wybierz przycisk **Dalej,** aby rozpocząć test.  Po zakończeniu testu zostanie wyświetlona tabela z wynikami testu (Pass/Fail/Warning).  W poniższym przykładzie przedstawiono wyniki testów testu maszyny Wirtualnej systemu Linux. 

![Wyniki testów certyfikacyjnych dla obrazu maszyny Wirtualnej systemu Linux](./media/publishvm_029.png)

Jeśli którykolwiek z testów nie powiedzie się, obraz *nie* jest certyfikowany. W takim przypadku przejrzyj wymagania i komunikaty o błędach, wykonuj wskazane zmiany i uruchom ponownie test. 

Po teście automatycznym należy podać dodatkowe informacje o obrazie maszyny Wirtualnej na ekranie **kwestionariusza.**  Zawiera dwie karty, które należy wykonać.  Karta **Ocena ogólna** zawiera pytania **prawda/fałsz,** natomiast **dostosowanie jądra** zawiera wiele pytań dotyczących zaznaczania i dowolnych form.  Wypełnij pytania na obu kartach, a następnie wybierz **przycisk Dalej**.

![Kwestionariusz narzędzia certyfikacji](./media/publishvm_030.png)

Ostatni ekran umożliwia dostarczenie dodatkowych informacji, takich jak informacje o dostępie do SSH dla obrazu maszyny Wirtualnej systemu Linux i wyjaśnienie wszelkich nieudanych ocen, jeśli szukasz wyjątków. 

Na koniec kliknij przycisk **Generuj raport,** aby pobrać wyniki testów i pliki dziennika dla wykonanych przypadków testowych oprócz odpowiedzi na kwestionariusz. Zapisz wyniki w tym samym kontenerze co wirtualne dyski wirtualne.

![Zapisywanie wyników testów certyfikacyjnych](./media/publishvm_031.png)


## <a name="next-steps"></a>Następne kroki

Następnie [wygenerujesz jednolite identyfikatory zasobów (URI) dla każdego dysku wirtualnego](./cpp-get-sas-uri.md) przesłanego do portalu Marketplace. 
