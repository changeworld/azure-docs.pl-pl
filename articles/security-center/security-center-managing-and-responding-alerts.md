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
ms.author: v-mohabe
ms.openlocfilehash: 39849514d772f128434daad590de22f941245af7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516096"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure
Ten dokument ułatwia zarządzanie alertami zabezpieczeń i reagowanie na nie przy użyciu usługi Azure Security Center.

> [!NOTE]
> Aby włączyć wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. Dostępna jest bezpłatna wersja próbna. W celu uaktualnienia wybierz warstwę cenową w [Zasadach zabezpieczeń](tutorial-security-policy.md). Aby dowiedzieć się więcej, zobacz [cennik usługi Azure Security Center](security-center-pricing.md).
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
* **Liczba**: Lista wszystkich alertów tego konkretnego typu, które zostały wykryte w określonym dniu.
* **Wykryte przez**: Usługa, która jest odpowiedzialna za wyzwalanie alertu.
* **Data**: Data wystąpienia zdarzenia.
* **Stan**: Bieżący stan dla tego alertu. Istnieją dwa typy stanów:
  * **Aktywne**: Wykryto alert zabezpieczeń.
  * **Odrzucono**: Alert zabezpieczeń został odrzucony przez użytkownika. Ten stan jest zwykle używany w przypadku alertów, które zostały zbadane i zostały uznane za faktyczne lub nie jest to rzeczywisty atak.
* **Ważność**: Poziom ważności, który może mieć wartość wysoki, średni lub niski.

> [!NOTE]
> Alerty zabezpieczeń wygenerowane przez usługę Security Center pojawią się również w dzienniku aktywności platformy Azure. Aby uzyskać więcej informacji o tym, jak uzyskać dostęp do dziennika aktywności platformy Azure, zobacz [View activity logs to audit actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) (Wyświetlanie dzienników aktywności w celu inspekcji akcji wykonywanych na zasobach).
>


### <a name="alert-severity"></a>Ważność alertu

-   **Wysoka**: Istnieje duże prawdopodobieństwo naruszenia bezpieczeństwa zasobu. Należy od razu przyjrzeć się do niego. Security Center ma wysoki poziom zaufania zarówno w złośliwym zamiarach, jak i w ustaleniach używanych do wystawiania alertu. Na przykład alert, który wykrywa wykonywanie znanego złośliwego narzędzia, takiego jak program mimikatz, typowego narzędzia używanego do kradzieży poświadczeń. 
-   **Średni**: Jest to prawdopodobnie podejrzane działanie, które może wskazywać na naruszenie bezpieczeństwa zasobu.
Stopień zaufania Security Center w analitycznym lub wyszukiwaniu jest średni, a wiarygodność złośliwego celu jest wysoka. Zazwyczaj mogą to być Uczenie maszynowe lub wykryte anomalie. Na przykład próba logowania z nietypowej lokalizacji.
-   **Niska**: Może to być niegroźne pozytywne lub zablokowany atak. 
    - Security Center nie ma wystarczającej pewności, że zamiar jest złośliwy, a działanie może być nieszkodliwe. Na przykład dziennik czyszczenie jest akcją, która może wystąpić, gdy osoba atakująca próbuje ukryć swoje ścieżki, ale w wielu przypadkach jest to procedura wykonywana przez administratorów.
    - Security Center zazwyczaj nie informuje użytkownika o zablokowaniu ataków, chyba że jest to interesujący przypadek, który sugerujemy. 
-   **Informacje**: Podczas przechodzenia do szczegółów zdarzenia związanego z bezpieczeństwem będą wyświetlane tylko alerty informacyjne lub użycie interfejsu API REST z określonym IDENTYFIKATORem alertu. Zdarzenie zwykle składa się z wielu alertów, które mogą być wyświetlane na własne potrzeby tylko w celu uzyskania informacji, ale w kontekście innych alertów może być zaufanego bliższego wyglądu.  

> [!NOTE]
> Jeśli korzystasz z wersji interfejsu API **2015-06-01-Preview** , istnieją różnice w tym, które typy ważności alarmów są stosowane do tego, co jest wymienione powyżej.  

### <a name="filtering-alerts"></a>Filtrowanie alertów
Alerty można filtrować na podstawie daty, stanu i ważności. Filtrowanie alertów może być przydatne w przypadku scenariuszy, w których należy zawęzić zakres wyświetlanych alertów zabezpieczeń. Możesz na przykład sprawdzić alerty zabezpieczeń, które wystąpiły w ciągu ostatnich 24 godzin, ponieważ badasz potencjalne naruszenie zabezpieczeń systemu.

1. Kliknij pozycję **Filtr** na stronie **Alerty zabezpieczeń**. Zostanie otwarty **Filtr**. Wybierz wartości daty, stanu i ważności, które chcesz wyświetlić.

    ![Filtrowanie alertów w usłudze Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Odpowiadanie na alerty zabezpieczeń
Wybierz alert zabezpieczeń, aby dowiedzieć się więcej o zdarzeniach, które go wywołały, oraz o czynnościach, które należy wykonać w celu wyeliminowania skutków ataku (jeśli ma to zastosowanie). Alerty zabezpieczeń są grupowane według typu i daty. Kliknięcie alertu zabezpieczeń powoduje otwarcie strony zawierającej listę pogrupowanych alertów.

![Odpowiadanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

W tym przypadku wyzwolone alerty dotyczą podejrzanego działania protokołu RDP (Remote Desktop Protocol). Pierwsza kolumna zawiera zaatakowane zasoby, druga przedstawia liczbę ataków na zasób, trzecia — czas ataku, czwarta — stan alertu, a piąta — ważność ataku. Po przejrzeniu tych informacji kliknij zaatakowany zasób.

![Sugestie dotyczące zalecanych działań związanych z alertami zabezpieczeń w Centrum zabezpieczeń Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

W polu **Opis** znajdziesz więcej szczegółowych informacji o tym zdarzeniu. Te dodatkowe szczegóły dotyczą przyczyn wyzwolenia alertu zabezpieczeń, zasobu docelowego, źródłowego adresu IP (jeśli ma to zastosowanie) i zaleceń dotyczących sposobu wyeliminowania skutków ataku.  W niektórych przypadkach źródłowy adres IP jest pusty (niedostępny), ponieważ nie wszystkie dzienniki zdarzeń zabezpieczeń systemu Windows obejmują adres IP.

Czynności naprawcze sugerowane w Centrum zabezpieczeń różnią się w zależności od alertu zabezpieczeń. W niektórych przypadkach do wykonania zalecanych czynności naprawczych trzeba będzie użyć innych funkcji platformy Azure. Na przykład korygowanie tego ataku nie zezwala na adres IP, który generuje ten atak, za pomocą [listy ACL sieci](../virtual-network/virtual-networks-acl.md) lub reguły [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules) . Aby uzyskać więcej informacji na temat różnych typów alertów, Odczytaj [typy alertów zabezpieczeń](security-center-alerts-overview.md#security-alert-types).

> [!NOTE]
> Usługa Security Center została wydana w ograniczonej wersji zapoznawczej z nowym zestawem funkcji wykrywania, które wykorzystują rekordy inspekcji, czyli wspólną platformę inspekcji, do wykrywania złośliwych zachowań na maszynach z systemem Linux. Wyślij [nam](mailto:ASC_linuxdetections@microsoft.com) wiadomość e-mail ze swoim identyfikatorem subskrypcji, aby dołączyć do tej wersji zapoznawczej.


## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób konfigurowania zasad zabezpieczeń w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
