---
title: Kroki publikowania aplikacji | Dokumentacja firmy Microsoft
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 0fc82229e158ed35b97203b11d08841c683c45ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398061"
---
<a name="app-publishing-steps"></a>Kroki publikowania aplikacji
====================

Aby rozpocząć proces publikowania, należy kliknąć "Publikuj" na karcie edytora.

![Przycisk CPP publikowania aplikacji](./media/d365-financials/image014.jpg)


Na karcie Stan zobaczysz kroki publikowania, wskazujące, gdzie jest oferty w procesie publikowania. W dowolnym momencie proces publikowania możesz zarejestrować się w i kliknij kartę oferuje wszystkie, aby wyświetlić najnowszy stan dla wszystkich Twoich ofert. Można kliknąć bezpośrednio na stan oferty i wyświetlić szczegółowe informacje, na którym jest oferty w procesie publikowania.

Przejdźmy teraz przez poszczególne kroki publikowania, omówić, co się dzieje w każdym kroku i jak długo należy oszacować każdego kroku spowoduje przejście.

![Diagram procesu publikowania](./media/d365-financials/image017.png)


**Weryfikowanie wymagań wstępnych**

Gdy klikniesz pozycję "Publikuj", zautomatyzowane kontrole ma miejsce, aby upewnić się, że wszystkie wymagane pola zostały wypełnione na ofertę. Jeśli wszystkie pola są wypełniane, obok pola zostanie wyświetlone ostrzeżenie i należy wypełnić ją dokładnie następnie ponownie kliknij przycisk "Publikuj".

Po zakończeniu tego kroku poprawnie wyświetlone wyskakujące okienko, pytaniem o adres e-mail, który będzie używany do wysyłania powiadomień do publikowania. Po przesłaniu adresu e-mail, ten krok jest pełny.


**Automatyczne stosowanie sprawdzania poprawności**

W tym kroku naszej usługi certyfikacji automatycznych sprawdza, czy rozszerzeń aplikacji z ich zawartość była zgodna z ofertą oferują metadanych. Zawsze upewnij się, że nazwa aplikacji, wersji, wydawcy i Identyfikatora odpowiada za pomocą podać w manifeście rozszerzenia o nazwie `app.json`.


**Testowanie poprawności dysku**

Jeśli użytkownik zgodził się skonfigurować wersję testową, tego etapu jest, gdy ustawienia użytkownika wersji testowej są prawidłowe.


**Sprawdzanie poprawności zarządzania potencjalnych klientów i rejestracji**

Na tym etapie Jeśli skonfigurowano funkcję powodować generowanie sprawdzamy, czy czy integracji usługi CRM działa, wysyłając testu potencjalnego klienta do CRM. Zostanie wyświetlony rekord o dane fikcyjne wypełnić CRM lub Azure Table po ukończeniu tego kroku. Całą dokumentację prowadzić generacji znajduje się tutaj.


**Tworzenie pakietów w usłudze AppSource**

Sprawdzane są Twoimi artefaktami szczegóły sklepu i pakietów w wersji zapoznawczej usługi AppSource jest generowany.


**Wyloguj się wydawcy**

Na tym etapie **Go Live** przycisk stanie się aktywny. Konieczne będzie teraz również link do oferty użytkownika (za pomocą usługi hidekey) w wersji zapoznawczej. Gdy jesteś zadowolony z wygląd w wersji zapoznawczej, kliknij przycisk Przejdź na żywo.
Należy pamiętać, nie powoduje to żądanie aplikacji na żywo w źródle aplikacji, ale zamiast tego wyzwala nasz proces weryfikacji wewnętrznych.


**Marketing i sprawdzanie poprawności technicznego aplikacji**

Ten krok jest, gdzie przeprowadzamy walidacji marketingowych i technicznych równolegle. Zapoznaj się [Lista kontrolna dotycząca przesyłania aplikacji](https://aka.ms/CheckBeforeYouSubmit) i [opracowywanie aplikacji dla Dynamics 365 for Finance and Operations oficjalny dokument](https://go.microsoft.com/fwlink/?linkid=841518) dokumenty wskazówki dotyczące obowiązkowe wymagania i zalecenia. W procesie weryfikacji obejmuje następujące czynności:
-  Współpraca z Tobą w żadnych oczekujących pytania i problemy.  
- Udostępnianie Data publikowania aplikacji i powiadomienie, gdy aplikacja została opublikowana. 
- umożliwiają pierwsze opinie dotyczące techniczne i marketingowe sprawdzania poprawności w ciągu 5 – 7 dni roboczych.

Te kroki zwykle mogą zająć dłużej niż przez tydzień, a nie ma potrzeby umożliwiające pozostanie stale zalogowany do portalu Cloud Partner.


**Publikowanie aplikacji przy użyciu usługi**

Oferty jest uaktualniany końcowe przetwarzanie. Aplikacja przeszła weryfikację techniczne i marketingowe, ale teraz musi przechodzić przez jakieś operacje przetwarzania końcowego do przygotowania źródłowej aplikacji.


**Live**

Twoja oferta jest teraz na żywo w usłudze AppSource, a klienci będą mogli przeglądać i wdrażanie aplikacji w ramach ich subskrypcji Microsoft Dynamics 365 Business Central. Otrzymasz wiadomość e-mail od nas informujący, że aplikacja zostanie on opublikowany w źródle aplikacji. W dowolnym momencie można kliknąć kartę wszystkie oferty i wyświetlany jest stan dla wszystkich Twoich ofert wymienione w prawej kolumnie. Kliknij stan, aby zobaczyć stan przepływu publikowania szczegóły oferty.


<a name="error-handling"></a>Obsługa błędów
--------------

W procesie publikowania może napotkać błąd. Jeśli okaże się błąd, otrzymasz wiadomość e-mail z powiadomieniem informujące o tym, że wystąpił błąd z instrukcjami dotyczącymi kolejnych kroków. Widać również błędy w dowolnym momencie w trakcie tego procesu, klikając kartę Stan. Zostanie wyświetlony, którego punktu w procesie, którego wystąpienia błędu oraz komunikat o błędzie konspektu, co musi zostać rozpoznane.

Jeśli wystąpią błędy w procesie publikowania jest wymagane, usuń te błędy, a następnie kliknij pozycję **Publikuj** ponowne uruchomienie procesu. Musi zaczynać się od początku publikowania kroki opisane w temacie **weryfikacji wymagań wstępnych** podczas ponownego publikowania po wszelkie poprawki błędów.

Jeśli występują problemy rozpoznawania błąd, należy otworzyć żądanie pomocy technicznej, aby uzyskać pomoc z inżynierami pomocy technicznej.


<a name="canceling-the-publishing-request"></a>Anulowanie żądania publikowania
--------------------------------

Możesz rozpocząć proces publikowania i potrzebują można anulować żądania. Żądaniu publikowania może anulować tylko, gdy żądanie opublikowania osiągnie kroku przygotowania wydawcy. Aby anulować, wybierz polecenie **Anuluj publikowanie**. Spowoduje to zresetowanie stanu publikowania do kroku 1, a następnie ponownie opublikować należy kliknąć przycisk Publikuj i postępuj zgodnie z instrukcjami w stanie.

![Publikowanie przycisk Anuluj](./media/d365-financials/image013.png)
