---
title: Przygotowywanie certyfikatów infrastruktury kluczy publicznych stosu Azure do wdrożenia systemów stosu Azure zintegrowanych | Dokumentacja firmy Microsoft
description: Opisuje sposób przygotowania certyfikatów PKI stosu Azure stosu Azure zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Przygotowanie do wdrożenia certyfikatów PKI stosu Azure
Pliki certyfikatów [uzyskany z urzędu certyfikacji wyboru](azure-stack-get-pki-certs.md) musi być zaimportowane i wyeksportowane z właściwościami dopasowania wymagań dotyczących certyfikatów Azure stosu.


## <a name="prepare-certificates-for-deployment"></a>Przygotowanie do wdrożenia certyfikatów
Do przygotowania i sprawdzania poprawności certyfikatów PKI stosu Azure, wykonaj następujące kroki: 

### <a name="import-the-certificate"></a>Zaimportuj certyfikat

1.  Skopiuj oryginalnej wersji certyfikatu [uzyskany z urzędu certyfikacji wyboru](azure-stack-get-pki-certs.md) do katalogu na hoście wdrożenia. 
  > [!WARNING]
  > Nie należy kopiować pliki, które już zostały zaimportowane, wyeksportowane lub zmienić w żadnym z plików udostępniane bezpośrednio przez urząd certyfikacji.

2.  Kliknij prawym przyciskiem myszy certyfikat, a następnie wybierz **Zainstaluj certyfikat** lub **Zainstaluj PFX** w zależności od tego, jak zostało dostarczone certyfikat z urzędu certyfikacji.

3. W **Kreatora importu certyfikatów**, wybierz pozycję **komputera lokalnego** jako lokalizacja importu. Wybierz opcję **Dalej**. Na poniższym ekranie kliknij przycisk Dalej ponownie.

    ![Lokalizacja importu komputera lokalnego](.\media\prepare-pki-certs\1.png)

4.  Wybierz **miejscu wszystkie certyfikatów w następującym magazynie** , a następnie wybierz **Zaufanie przedsiębiorstwa** jako lokalizacji. Kliknij przycisk **OK** aby zamknąć okno dialogowe Wybieranie magazynu certyfikatów, a następnie **dalej**.

    ![Konfigurowanie magazynu certyfikatów](.\media\prepare-pki-certs\3.png)

    a. Jeśli importujesz PFX zostaną wyświetlone dodatkowe okno. Na **ochrony klucza prywatnego** wprowadź hasło plików certyfikatów, a następnie Włącz **Oznacz ten klucz jako eksportowalny. Dzięki temu można utworzyć kopię zapasową kluczy lub w późniejszym czasie** opcji. Wybierz opcję **Dalej**.

    ![Oznacz ten klucz jako eksportowalny](.\media\prepare-pki-certs\2.png)

5. Kliknij przycisk Zakończ, aby zakończyć importu.

### <a name="export-the-certificate"></a>Eksportowanie certyfikatu

Otwórz konsolę menedżera certyfikatów i nawiązać połączenia z magazynu certyfikatów komputera lokalnego.

1. Otwórz konsolę zarządzania firmy Microsoft, w systemie Windows 10 kliknij prawym przyciskiem myszy Start Menu, a następnie kliknij przycisk Uruchom. Typ **mmc** kliknij przycisk ok.

2. Kliknij plik, Dodaj/Usuń przystawkę, a następnie wybierz opcję certyfikaty, kliknij przycisk Dodaj.

    ![Dodawanie przystawki Certyfikaty](.\media\prepare-pki-certs\mmc-2.png)
 
3. Wybierz konto komputera, kliknij przycisk Dalej, a następnie wybierz komputer lokalny, a następnie Zakończ. Kliknij przycisk ok, aby zamknąć stronę Dodaj/Usuń przystawkę.

    ![Dodawanie przystawki Certyfikaty](.\media\prepare-pki-certs\mmc-3.png)

4. Przeglądaj, aby certyfikaty > Zaufanie przedsiębiorstwa > Lokalizacja certyfikatów. Upewnij się, zostanie wyświetlony certyfikat po prawej stronie.

5. W konsoli Menedżera zadań pasek certyfikatu wybierz **akcje** > **wszystkie zadania** > **wyeksportować**. Wybierz opcję **Dalej**.

  > [!NOTE]
  > W zależności od tego, ile stosu Azure certyfikaty się, że użytkownik może być konieczne ukończenie tego procesu więcej niż raz.

4. Wybierz **tak, Eksportuj klucz prywatny**, a następnie kliknij przycisk **dalej**.

5. W sekcji Format pliku eksportu wybierz **Eksportuj wszystkie właściwości rozszerzone** , a następnie kliknij przycisk **dalej**.

6. Wybierz **hasło** i podaj hasło dla certyfikatów. Zapamiętaj to hasło, ponieważ jest używany jako parametr wdrożenia. Wybierz opcję **Dalej**.

7. Wybierz nazwę pliku i lokalizację pliku pfx można wyeksportować. Wybierz opcję **Dalej**.

8. Wybierz pozycję **Finish** (Zakończ).

## <a name="next-steps"></a>Kolejne kroki
[Sprawdź poprawność certyfikatów PKI](azure-stack-validate-pki-certs.md)