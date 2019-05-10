---
title: Zarządzanie alertami zabezpieczeń w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu funkcji Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: 582912160c8ed514401be3522e52dcc6eb45d263
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235768"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure
Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu usługi Azure Security Center.

> [!NOTE]
> Aby włączyć wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Bezpłatna wersja próbna jest dostępna. W celu uaktualnienia wybierz warstwę cenową w [Zasadach zabezpieczeń](tutorial-security-policy.md). Aby dowiedzieć się więcej, zobacz [cennik usługi Azure Security Center](security-center-pricing.md).
>
>

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku.


> [!NOTE]
> Aby uzyskać więcej informacji na temat sposobu działania funkcji wykrywania usługi Security Center, przeczytaj [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Zarządzanie alertami zabezpieczeń
Bieżące alerty można przeglądać przy użyciu kafelka **Alerty zabezpieczeń**. Wykonaj poniższe kroki, aby wyświetlić więcej szczegółowych informacji dotyczących poszczególnych alertów:

1. Na pulpicie nawigacyjnym Centrum zabezpieczeń zobaczysz kafelek **Alerty zabezpieczeń**.

    ![Kafelek Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Kliknij kafelek, aby otworzyć stronę **Alerty zabezpieczeń** i zobaczyć więcej szczegółowych informacji o alertach.

   ![Alerty zabezpieczeń w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

W dolnej części tej strony znajdują się szczegółowe informacje o każdym alercie. Aby posortować dane, kliknij kolumnę, według której chcesz wykonać sortowanie. Poniżej znajdują się definicje poszczególnych kolumn:

* **Opis**: Krótki opis alertu.
* **Liczba**: Lista wszystkich alertów określonego typu, które zostały wykryte w określonym dniu.
* **Wykryte przez**: Usługa, która była odpowiedzialna za wyzwolenie alertu.
* **Data**: Data, które wystąpiło zdarzenie.
* **Stan**: Bieżący stan alertu. Istnieją dwa typy stanów:
  * **Aktywne**: Alert zabezpieczeń został wykryty.
  * **Odrzucono**: Alert zabezpieczeń został odrzucony przez użytkownika. Ten stan jest zwykle używana w przypadku alertów, które zostały zbadane i zastosowanymi środkami zaradczymi lub został odnaleziony, aby nie dotyczył rzeczywistego ataku.
* **Ważność**: Poziom ważności, który może być wysoki, średni lub niski.

> [!NOTE]
> Alerty zabezpieczeń wygenerowane przez usługę Security Center pojawią się również w dzienniku aktywności platformy Azure. Aby uzyskać więcej informacji o tym, jak uzyskać dostęp do dziennika aktywności platformy Azure, zobacz [View activity logs to audit actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) (Wyświetlanie dzienników aktywności w celu inspekcji akcji wykonywanych na zasobach).
>


### <a name="alert-severity"></a>Ważność alertu

-   **Wysoka**: Istnieje wysokie prawdopodobieństwo, że zasób zostanie naruszone. Następnie od razu należy zwrócić uwagę do niego. Usługa Security Center ma o dużej pewności w obu złośliwego działania i ustalenia używany do wysyłania alertu. Na przykład alert, który wykrywa wykonywania znane złośliwe narzędzia, takiego jak program Mimikatz, popularnego narzędzia używane do kradzieży poświadczeń. 
-   **Średnia**: Jest to prawdopodobnie podejrzanych działań, które mogą wskazywać, że zasób zostanie naruszone.
Security Center w analitycznych lub wyszukiwaniu UFNOŚĆ średni i UFNOŚĆ złośliwego działania średniej lub dużej. Są to zazwyczaj będzie uczenia maszynowego ani rozwiązaniami do wykrywania anomalii na podstawie. Na przykład logowanie próba z nietypowej lokalizacji.
-   **Niska**: Może to być nieszkodliwe dodatni lub blokowania ataków. 
    - Usługa Security Center nie jest wystarczająco pewność, że celem jest złośliwego i działanie może być nieszkodliwe. Na przykład Wyczyść dziennik jest akcję, która może się zdarzyć, gdy osoba atakująca próbuje użyć w celu ukrycia śladów ich działania, ale w wielu przypadkach jest operacją procedury wykonywane przez administratorów.
    - Usługa Security Center nie zwykle zorientować się, gdy ataków zostały zablokowane, chyba że jest to interesujące takim Sugerujemy, że masz możliwość przejrzenia. 
-   **Informacyjny**: Widoczne są tylko alerty informacyjne podczas przechodzenia do szczegółów w przypadku wystąpienia zdarzenia zabezpieczeń, czy przy użyciu interfejsu API REST z określonym identyfikatorem alertu. Zdarzenia zwykle składa się z liczby alertów, niektóre z nich mogą być wyświetlane w ich własnych tylko do informacyjna, ale w kontekście innych alertów może być Alberta bliżej.  

> [!NOTE]
> Jeśli używasz **2015-06-01-preview** wersji interfejsu API, a następnie istnieją różnice w alarmu, które typy ważności są stosowane do scenariuszy, do których, niż wymienione powyżej.  

### <a name="filtering-alerts"></a>Filtrowanie alertów
Alerty można filtrować na podstawie daty, stanu i ważności. Filtrowanie alertów może być przydatne w przypadku scenariuszy, w których należy zawęzić zakres wyświetlanych alertów zabezpieczeń. Możesz na przykład sprawdzić alerty zabezpieczeń, które wystąpiły w ciągu ostatnich 24 godzin, ponieważ badasz potencjalne naruszenie zabezpieczeń systemu.

1. Kliknij pozycję **Filtr** na stronie **Alerty zabezpieczeń**. Zostanie otwarty **Filtr**. Wybierz wartości daty, stanu i ważności, które chcesz wyświetlić.

    ![Filtrowanie alertów w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Odpowiadanie na alerty zabezpieczeń
Wybierz alert zabezpieczeń, aby dowiedzieć się więcej o zdarzeniach, które go wywołały, oraz o czynnościach, które należy wykonać w celu wyeliminowania skutków ataku (jeśli ma to zastosowanie). Alerty zabezpieczeń są grupowane według typu i daty. Kliknięcie alertu zabezpieczeń powoduje otwarcie strony zawierającej listę pogrupowanych alertów.

![Odpowiadanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

W tym przypadku wyzwolone alerty dotyczą podejrzanego działania protokołu RDP (Remote Desktop Protocol). Pierwsza kolumna zawiera zaatakowane zasoby, druga przedstawia liczbę ataków na zasób, trzecia — czas ataku, czwarta — stan alertu, a piąta — ważność ataku. Po przejrzeniu tych informacji kliknij zaatakowany zasób.

![Sugestie dotyczące zalecanych działań związanych z alertami zabezpieczeń w Centrum zabezpieczeń Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

W polu **Opis** znajdują się dalsze szczegółowe informacje na temat danego zdarzenia. Te dodatkowe szczegóły dotyczą przyczyn wyzwolenia alertu zabezpieczeń, zasobu docelowego, źródłowego adresu IP (jeśli ma to zastosowanie) i zaleceń dotyczących sposobu wyeliminowania skutków ataku.  W niektórych przypadkach źródłowy adres IP jest pusty (niedostępny), ponieważ nie wszystkie dzienniki zdarzeń zabezpieczeń systemu Windows obejmują adres IP.

Czynności naprawcze sugerowane w Centrum zabezpieczeń różnią się w zależności od alertu zabezpieczeń. W niektórych przypadkach do wykonania zalecanych czynności naprawczych trzeba będzie użyć innych funkcji platformy Azure. Na przykład aby wyeliminować skutki takiego ataku, należy utworzyć listę niedozwolonych adresów IP generujących atak przy użyciu [sieciowej listy ACL](../virtual-network/virtual-networks-acl.md) lub reguły [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules). Aby znaleźć więcej informacji na temat różnych typów alertów, przeczytaj artykuł [Alerty zabezpieczeń według typu w usłudze Azure Security Center](security-center-alerts-type.md).

> [!NOTE]
> Usługa Security Center została wydana w ograniczonej wersji zapoznawczej z nowym zestawem funkcji wykrywania, które wykorzystują rekordy inspekcji, czyli wspólną platformę inspekcji, do wykrywania złośliwych zachowań na maszynach z systemem Linux. Wyślij [nam](mailto:ASC_linuxdetections@microsoft.com) wiadomość e-mail ze swoim identyfikatorem subskrypcji, aby dołączyć do tej wersji zapoznawczej.


## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób konfigurowania zasad zabezpieczeń w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
