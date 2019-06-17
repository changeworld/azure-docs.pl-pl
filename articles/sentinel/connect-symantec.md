---
title: Połącz dane firmy Symantec ICDX przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych firmy Symantec ICDX przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: rkarlin
ms.openlocfilehash: 3b21371d6321b208b19ca8b2524308736c3ceca9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244344"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Połącz urządzenie ICDX firmy Symantec 

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik ICDX firmy Symantec umożliwia łatwe łączenie wszystkich Twojej firmy Symantec dzienników rozwiązań dotyczących zabezpieczeń, za pomocą usługi Azure przez wartownika, aby wyświetlić pulpity nawigacyjne, tworzyć niestandardowe alerty i lepsze badanie. To zapewnia lepszy wgląd w sieci swojej organizacji i zwiększa możliwości operacji zabezpieczeń. Integracja między ICDX firmy Symantec i platformy Azure przez wartownika korzysta z interfejsu API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Skonfigurować i połączyć z ICDX firmy Symantec 

Integrację i wyeksportuj dzienniki bezpośrednio na platformie Azure przez wartownika ICDX firmy Symantec.

1. Otwórz konsolę zarządzania ICDX, aby dodać usługi przesyłania dalej z Microsoft Azure przez wartownika (usługi Log Analytics).
2. Na pasku nawigacyjnym ICDx kliknij **konfiguracji**. 
3. W górnej części **konfiguracji** ekranu, kliknij przycisk **usług przesyłania dalej**.
4. W obszarze **usług przesyłania dalej**, obok Microsoft Azure przez wartownika (usługi Log Analytics), kliknij **Dodaj**. 
4. W **Microsoft Azure (usługi Log Analytics) przez wartownika** okna, kliknij przycisk **Pokaż zaawansowane**. 
5. W górnej części rozwiniętym okienku okna Microsoft Azure przez wartownika (usługi Log Analytics), wykonaj następujące czynności:
    -   **Nazwa**: Wpisz nazwę dla usługi przesyłania dalej, który ma nie więcej niż 30 znaków. Wybierz unikatową nazwę opisową. Nazwa ta pojawia się na liście usług przesyłania dalej ACS na **konfiguracji** ekranu i na pulpitach nawigacyjnych na **pulpit nawigacyjny** ekranu. Na przykład: Microsoft Azure Log Analytics Wschodnia. To pole jest wymagane.
    -   **Opis**: Wpisz opis usługi przesyłania dalej. Opis ten pojawia się również na liście usług przesyłania dalej w **konfiguracji** ekranu. Zawierają szczegółowe informacje, takie jak typ zdarzenia przesyłane dalej i grupy, która musi sprawdzić dane.
    -   **Typ uruchomienia**: Wybierz metodę uruchomienia dla konfiguracji usługi przesyłania dalej. Opcje są ręczne i automatyczne.<br>Wartość domyślna to automatycznie. 
6. W obszarze **zdarzenia**, wykonaj następujące czynności: 
    - **Źródło**: Wybierz co najmniej jeden archiwów, z którego ma zostać przekazywania zdarzeń. Możesz wybrać archiwa aktywnego modułu zbierającego (w tym wspólnego archiwum) oddzielone archiwa modułu zbierającego (czyli archiwa dla modułów zbierających dzienniki, które zostały usunięte), archiwum do odbiornika ICDx lub archiwum systemu. <br>Wartość domyślna to typowe archiwum.
      > [!NOTE]
      > Archiwum do odbiornika ICDx są przedstawione oddzielnie, według nazwy. 
 
    - **Filtr**: Dodaj filtr, który określa podzbiór zdarzeń do przesyłania dalej. Wykonaj jedną z następujących czynności:
        - Aby wybrać warunek filtru, kliknij typ, atrybut, Operator i wartość. 
        - W polu filtrowania należy sprawdzić warunku filtru. Można go edytować bezpośrednio w polu lub usuń go zgodnie z potrzebami.
        - Kliknij przycisk oraz lub lub dodać do warunku filtru.
        - Możesz również kliknąć zapisane zapytania, aby zastosować zapisane zapytanie.
    - **Uwzględnione atrybuty**: Wpisz rozdzielana przecinkami lista atrybutów, które mają zostać objęte przekazane dane. Uwzględnione atrybuty mają pierwszeństwo przed atrybutami wykluczone.
    - **Wykluczone atrybuty**: Wpisz rozdzielana przecinkami lista atrybutów, które mają zostać wykluczone z danych przesyłanych dalej.
    - **Wielkość partii**: Wybierz numer zdarzenia do wysłania na partię. Dostępne opcje to 10, 50, 100, 500 i 1000.<br>Wartość domyślna to 100. 
    - **Limit szybkości**: Wybierz szybkość jaką zdarzenia są przesyłane, wyrażone jako zdarzeń na sekundę. Możliwości są nieograniczone, 500, 1000, 5000, 10000. <br> Wartość domyślna to 5000. 
7. W obszarze **docelowego Azure**, wykonaj następujące czynności: 
    - **Identyfikator obszaru roboczego**: Wklej identyfikator obszaru roboczego z poniższych. To pole jest wymagane.
    - **Klucz podstawowy**: Wklej klucz podstawowy z poniższych. To pole jest wymagane.
    - **Nazwa dziennika niestandardowego**: Wpisz nazwę dziennika niestandardowego w Microsoft Azure portal roboczym usługi Log Analytics przechodząc do przesyłania dalej zdarzeń. Wartość domyślna to SymantecICDx. To pole jest wymagane.
8. Kliknij przycisk *Zapisz* Zakończ konfigurację usługi przesyłania dalej. 
9. Aby uruchomić usługę przesyłania dalej, w obszarze **opcje**, kliknij przycisk **więcej** a następnie **Start**.
10. Aby użyć odpowiednich schematu w usłudze Log Analytics dla zdarzeń ICDX firmy Symantec, możesz wyszukać **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Zweryfikuj łączność

Może upłynąć zgłaszane 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób nawiązać Azure przez wartownika ICDX firmy Symantec. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

