---
title: Przygotować certyfikaty infrastruktury klucza publicznego usługi Azure Stack dla usługi Azure Stack zintegrowane systemy wdrażania lub klucza tajnego obrotu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak przygotować certyfikaty infrastruktury kluczy publicznych do usługi Azure Stack w systemach zintegrowanych w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: fab7ead59ce8d7e12d145095c64cedf4234e6233
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656723"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Przygotowywanie certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack do użycia w wdrożenia lub obrotu

Pliki certyfikatów [uzyskany z urzędu certyfikacji wybór](azure-stack-get-pki-certs.md) musi być importowane i wyeksportowane z właściwościami dopasowania wymagania dotyczące certyfikatu usługi Azure Stack.

## <a name="prepare-certificates-for-deployment"></a>Przygotowywanie certyfikatów do wdrożenia

Wykonaj następujące kroki, aby przygotować i sprawdzenia poprawności certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack, które będą używane do wdrożenia nowego środowiska usługi Azure Stack lub związany z rotacją kluczy tajnych w istniejącym środowisku usługi Azure Stack: 

### <a name="import-the-certificate"></a>Zaimportuj certyfikat

1.  Skopiuj oryginalnej wersji certyfikatu [uzyskany z urzędu certyfikacji wybór](azure-stack-get-pki-certs.md) do katalogu na hoście wdrożenia. 
  > [!WARNING]
  > Nie należy kopiować pliki, które zostały już zaimportowane, wyeksportowane lub zmienić w dowolny sposób przy użyciu plików podawana bezpośrednio przez urząd certyfikacji.

1.  Kliknij prawym przyciskiem myszy certyfikat i wybierz pozycję **Zainstaluj certyfikat** lub **Zainstaluj PFX** w zależności od tego, jak certyfikatu zostało dostarczone z urzędu certyfikacji.

1. W **Kreatora importu certyfikatów**, wybierz opcję **komputera lokalnego** jako lokalizację importu. Wybierz opcję **Dalej**. Na poniższym ekranie kliknij przycisk Dalej ponownie.

    ![Lokalizacja importu komputera lokalnego](./media/prepare-pki-certs/1.png)

1.  Wybierz **Umieść wszystkie certyfikaty w następującym magazynie** , a następnie wybierz **Zaufanie przedsiębiorstwa** jako lokalizację. Kliknij przycisk **OK** aby zamknąć okno dialogowe Wybieranie magazynu certyfikatów i następnie **dalej**.

    ![Konfigurowanie magazynu certyfikatów](./media/prepare-pki-certs/3.png)

    a. Jeśli importujesz plik PFX, zostaną wyświetlone dodatkowe okno. Na **ochrona klucza prywatnego** strony, wprowadź hasło dla certyfikatu plików, a następnie Włącz **Oznacz ten klucz jako eksportowalny. Dzięki temu można utworzyć kopię zapasową kluczy lub w późniejszym czasie** opcji. Wybierz opcję **Dalej**.

    ![Oznacz ten klucz jako eksportowalny](./media/prepare-pki-certs/2.png)

1. Kliknij przycisk Zakończ, aby ukończyć importowanie.

### <a name="export-the-certificate"></a>Eksportowanie certyfikatu

Otwórz konsolę menedżera certyfikatów konsoli MMC i nawiązać połączenie z magazynu certyfikatów komputera lokalnego.

1. Otwórz konsolę zarządzania firmy Microsoft, w systemie Windows 10 kliknij prawym przyciskiem myszy Start Menu, a następnie kliknij przycisk Uruchom. Typ **mmc** kliknij przycisk ok.

1. Kliknij plik, Dodaj/Usuń przystawkę, a następnie wybierz certyfikat, kliknij przycisk Dodaj.

    ![Dodaj przystawkę Certyfikaty](./media/prepare-pki-certs/mmc-2.png)
 
1. Wybierz konto komputera, kliknij przycisk Dalej, a następnie wybierz komputer lokalny, a następnie Zakończ. Kliknij przycisk ok, aby zamknąć stronę Dodawanie/Usuwanie przystawki.

    ![Dodaj przystawkę Certyfikaty](./media/prepare-pki-certs/mmc-3.png)

1. Przejdź do certyfikatów > Zaufanie przedsiębiorstwa > Lokalizacja certyfikatu. Sprawdź, czy widzisz certyfikatu po prawej stronie.

1. W konsoli Menedżera zadań pasek certyfikat, wybierz **akcje** > **wszystkie zadania** > **wyeksportować**. Wybierz opcję **Dalej**.

  > [!NOTE]
  > Zależności od tego, ile usługi Azure Stack certyfikaty, że użytkownik może być konieczne wykonanie tego procesu więcej niż jeden raz.

1. Wybierz **tak, Eksportuj klucz prywatny**, a następnie kliknij przycisk **dalej**.

1. W sekcji Format pliku eksportu:
    
    - Wybierz **, jeśli jest to możliwe, Dołącz wszystkie certyfikaty w certyfikacie**.  
    - Wybierz **Eksportuj wszystkie właściwości rozszerzone**.  
    - Wybierz **Włącz prywatność certyfikatu**.  
    - Kliknij przycisk **Dalej**.  
    
    ![Kreator eksportu certyfikatów przy użyciu wybrane opcje](./media/prepare-pki-certs\azure-stack-save-cert.png)

1. Wybierz **hasło** i podaj hasło dla certyfikatów. Utwórz hasło, które spełnia następujące wymagania dotyczące złożoności hasła. Długość co najmniej osiem znaków. Hasło zawiera co najmniej trzech spośród następujących: wielkie litery, małe litery, cyfry od 0 do 9, znaków specjalnych, alfabetycznym znak, który nie jest wielką ani małe litery. Zanotuj hasło. Użyjesz go jako parametr wdrożenia.

1. Wybierz opcję **Dalej**.

1. Wybierz nazwę pliku i lokalizację pliku pfx można wyeksportować. Wybierz opcję **Dalej**.

1. Wybierz pozycję **Finish** (Zakończ).

## <a name="next-steps"></a>Kolejne kroki

[Sprawdzanie poprawności certyfikatów PKI](azure-stack-validate-pki-certs.md)