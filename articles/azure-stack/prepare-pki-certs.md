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
ms.openlocfilehash: dadb443f8b7739e3a18c0d3beb558d8c42e9d19c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Przygotowanie do wdrożenia certyfikatów PKI stosu Azure
Pliki certyfikatów [uzyskany z urzędu certyfikacji wyboru](azure-stack-get-pki-certs.md) musi być zaimportowane i wyeksportowane z właściwościami dopasowania wymagań dotyczących certyfikatów Azure stosu.


## <a name="prepare-certificates-for-deployment"></a>Przygotowanie do wdrożenia certyfikatów
Do przygotowania i sprawdzania poprawności certyfikatów PKI stosu Azure, wykonaj następujące kroki: 

1.  Skopiuj oryginalnej wersji certyfikatu [uzyskany z urzędu certyfikacji wyboru](azure-stack-get-pki-certs.md) do katalogu na hoście wdrożenia. 
  > [!WARNING]
  > Nie należy kopiować pliki, które już zostały zaimportowane, wyeksportowane lub zmienić w żadnym z plików udostępniane bezpośrednio przez urząd certyfikacji.

2.  Importowanie certyfikatów do magazynu certyfikatów komputera lokalnego:

    a.  Kliknij prawym przyciskiem myszy certyfikat, a następnie wybierz **Zainstaluj PFX**.

    b.  W **Kreatora importu certyfikatów**, wybierz pozycję **komputera lokalnego** jako lokalizacja importu. Wybierz opcję **Dalej**.

    ![Lokalizacja importu komputera lokalnego](.\media\prepare-pki-certs\1.png)

    c.  Wybierz **dalej** na **wybierz plik do zaimportowania** strony.

    d.  Na **ochrony klucza prywatnego** wprowadź hasło plików certyfikatów, a następnie Włącz **Oznacz ten klucz jako eksportowalny. Dzięki temu można utworzyć kopię zapasową kluczy lub w późniejszym czasie** opcji. Wybierz opcję **Dalej**.

    ![Oznacz ten klucz jako eksportowalny](.\media\prepare-pki-certs\2.png)

    e.  Wybierz **miejscu wszystkie certyfikatów w następującym magazynie** , a następnie wybierz **Zaufanie przedsiębiorstwa** jako lokalizacji. Kliknij przycisk **OK** aby zamknąć okno dialogowe Wybieranie magazynu certyfikatów, a następnie **dalej**.

    ![Konfigurowanie magazynu certyfikatów](.\media\prepare-pki-certs\3.png)

  f.    Kliknij przycisk **Zakończ** aby zakończyć działanie Kreatora importu certyfikatów.

  g.    Należy powtórzyć dla wszystkich certyfikatów, które udostępniasz dla danego wdrożenia.

3. Wyeksportuj certyfikat do formatu pliku PFX z wymaganiami Azure stosu:

  a.    Otwórz konsolę menedżera certyfikatów i nawiązać połączenia z magazynu certyfikatów komputera lokalnego.

  b.    Przejdź do **Zaufanie przedsiębiorstwa** katalogu.

  c.    Wybierz jeden z certyfikatów, które zostały zaimportowane w kroku 2.

  d.    W konsoli Menedżera zadań pasek certyfikatu wybierz **akcje** > **wszystkie zadania** > **wyeksportować**.

  e.    Wybierz opcję **Dalej**.

  f.    Wybierz **tak, Eksportuj klucz prywatny**, a następnie kliknij przycisk **dalej**.

  g.    W sekcji Format pliku eksportu wybierz **Eksportuj wszystkie właściwości rozszerzone** , a następnie kliknij przycisk **dalej**.

  h.    Wybierz **hasło** i podaj hasło dla certyfikatów. Zapamiętaj to hasło, ponieważ jest używany jako parametr wdrożenia. Wybierz opcję **Dalej**.

  i.    Wybierz nazwę pliku i lokalizację pliku pfx można wyeksportować. Wybierz opcję **Dalej**.

  j.    Wybierz pozycję **Finish** (Zakończ).

  k.    Powtórz ten proces dla wszystkich certyfikatów, zaimportowane do wdrożenia w kroku 2.

## <a name="next-steps"></a>Kolejne kroki
[Sprawdź poprawność certyfikatów PKI](validate-pki-certs.md)