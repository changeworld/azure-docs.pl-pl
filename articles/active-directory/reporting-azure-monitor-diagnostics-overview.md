---
title: Dzienniki aktywności usługi Azure Active Directory w usłudze Azure Monitor (wersja zapoznawcza) | Microsoft Docs
description: Omówienie dzienników aktywności usługi Azure Active Directory w usłudze Azure Monitor (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240112"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Dzienniki aktywności usługi Azure Active Directory w usłudze Azure Monitor (wersja zapoznawcza)

Możesz teraz kierować dzienniki usługi Azure AD na swoje własne konto magazynu lub do centrum zdarzeń przy użyciu usługi Azure Monitor. Publiczna wersja zapoznawcza dzienników usługi Azure Active Directory w usłudze Azure Monitor umożliwia:

* Archiwizowanie dzienników inspekcji dla konta usługi Azure, co pozwala przechowywać dane przez długi czas
* Przesyłanie strumieniowe dzienników inspekcji do centrum zdarzeń platformy Azure w celu analizy za pomocą popularnych narzędzi SIEM, takich jak Splunk i QRadar
* Integrowanie dzienników inspekcji z niestandardowymi rozwiązaniami dziennika przez przesyłanie ich strumieniowo do centrum zdarzeń

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Obsługiwane raporty

Przy użyciu tej funkcji można przekierowywać dzienniki aktywności dotyczące inspekcji i dzienniki aktywności dotyczące logowań na konto usługi Azure Storage, do centrum zdarzeń lub niestandardowego rozwiązania. 

* **Dzienniki inspekcji** — [raport działań dotyczący dzienników inspekcji](active-directory-reporting-activity-audit-logs.md) zapewnia dostęp do historii wszystkich zadań wykonanych w dzierżawie.
* **Logowania** — przy użyciu [raportu działań dotyczącego logowań](active-directory-reporting-activity-sign-ins.md) można określić, kto wykonał zadania zgłoszone w raporcie dzienników inspekcji.

> [!NOTE]
> Dzienniki aktywności inspekcji i logowania związane z funkcjami B2C nie są obecnie obsługiwane.
>

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* [Licencja](https://azure.microsoft.com/pricing/details/active-directory/) usługi Azure AD — wersja Bezpłatna, Podstawowa, Premium 1 lub Premium 2, aby uzyskać dostęp do dzienników usługi Azure AD w witrynie Azure Portal. 

W zależności od tego, gdzie chcesz przekierować dane dziennika inspekcji, potrzebne są:

* Konto usługi Azure Storage, do którego masz uprawnienia *ListKeys*. Zalecamy używanie konta magazynu ogólnego, a nie konta magazynu obiektów blob. Aby uzyskać informacje o cenach magazynu, zobacz się z [kalkulator cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Przestrzeń nazw usługi Azure Event Hubs, w celu integracji z rozwiązaniami innych firm.

> [!NOTE]
> Musisz być *administratorem globalnym* lub *administratorem zabezpieczeń* w dzierżawie usługi Azure AD, aby mieć dostęp do dzienników aktywności usługi Azure AD.
>

## <a name="cost-considerations"></a>Kwestie związane z kosztami

Jeśli masz już licencję usługi Azure AD, potrzebujesz subskrypcji platformy Azure do skonfigurowania magazynu konta i centrum zdarzeń. Subskrypcja platformy Azure jest dostępna bezpłatnie, ale trzeba płacić za korzystanie z zasobów platformy Azure, w tym konto magazynu używane na potrzeby archiwizacji oraz centrum zdarzeń używane do przesyłania strumieniowego. Ilość danych, a w związku z tym koszt, różnią się znacznie w zależności od rozmiaru dzierżawy. 

### <a name="storage-size-for-activity-logs"></a>Rozmiar magazynu dla dzienników aktywności

Każde zdarzenie dziennika inspekcji używa około 2 KB magazynu danych. A więc w przypadku dzierżawy z 100 000 użytkowników, którzy generują około 1,5 miliona zdarzeń dziennie, będziesz potrzebować około 3 GB magazynu danych na dzień. Ponieważ operacje zapisu są przetwarzane wsadowo (w partiach) w około 5-minutowych odstępach, możesz oczekiwać około 9000 operacji zapisu miesięcznie. Poniższa tabela zawiera przybliżone oszacowanie kosztów w zależności od rozmiaru dzierżawy w przypadku konta magazynu ogólnego przeznaczenia w wersji 2 w regionie Zachodnie stany USA z okresem przechowywania co najmniej jeden rok. Użyj [kalkulatora cen usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/), aby utworzyć bardziej dokładne oszacowanie dla ilości danych, którą przewidujesz dla aplikacji. Każde zdarzenie dziennika inspekcji używa około 2 KB magazynu danych. A więc w przypadku dzierżawy z 100 000 użytkowników, którzy generują około 1,5 miliona zdarzeń dziennie, będziesz potrzebować około 3 GB magazynu danych na dzień. Ponieważ operacje zapisu są przetwarzane wsadowo (w partiach) w około 5-minutowych odstępach, możesz oczekiwać około 9000 operacji zapisu miesięcznie. Poniższa tabela zawiera przybliżone oszacowanie kosztów w zależności od rozmiaru dzierżawy w przypadku konta magazynu ogólnego przeznaczenia w wersji 2 w regionie Zachodnie stany USA z okresem przechowywania co najmniej jeden rok. Użyj [kalkulatora cen usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/), aby utworzyć bardziej dokładne oszacowanie dla ilości danych, którą przewidujesz dla aplikacji. 

| Kategoria dziennika | Liczba użytkowników | Liczba zdarzeń na dzień | Przybliżone ilości danych na miesiąc | Przybliżony koszt na miesiąc | Przybliżony koszt na rok |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Inspekcja | 100 000 | 1,5 mln | 90 GB | 1,93 USD | 23,12 USD |
| Inspekcja | 1000 | 15000 | 900 MB | 0,02 USD | 0,24 USD |
| Logowania | 1000 | 34800 | 4 GB | 0,13 USD | 1,56 USD |
| Logowania | 100 000 | 15 mln | 1,7 TB | 35,41 USD | 424,92 USD | 


### <a name="event-hub-messages-for-activity-logs"></a>Komunikaty centrum zdarzeń dotyczące dzienników aktywności

Zdarzenia są przetwarzane w partiach w około 5-minutowych odstępach i wysyłane jako pojedynczy komunikat zawierający wszystkie zdarzenia, które wystąpiły w tym przedziale czasu. Maksymalny rozmiar komunikatu w centrum zdarzeń wynosi 256 kB, a jeśli rozmiar wszystkich komunikatów w danym przedziale czasu przekracza ten wolumen, wysyłane jest wiele komunikatów. 

Na przykład w przypadku dużej dzierżawy z ponad 100 000 użytkowników występuje zwykle 18 zdarzeń na sekundę, co równa się 5400 zdarzeń co 5 minut. Ponieważ dzienniki inspekcji mają około 2 KB na zdarzenie, to równa się 10,8 MB danych, a zatem w tym 5-minutowym interwale do centrum zdarzeń zostaną wysłane 43 wiadomości. Poniższa tabela zawiera przybliżony koszt dla podstawowego centrum zdarzeń w regionie Zachodnie stany USA w zależności od ilości danych zdarzeń. Użyj [kalkulatora cen usługi Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/) w celu obliczenia dokładnego oszacowania dla ilości danych przewidywanej w przypadku danej.

| Kategoria dziennika | Liczba użytkowników | Liczba zdarzeń/s | Liczba zdarzeń na okres 5 minut | Wolumen na interwał | Liczba komunikatów na interwał | Liczba komunikatów na miesiąc | Przybliżony koszt na miesiąc |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Inspekcja | 100 000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Inspekcja | 1000 | 0.1 | 52 | 104 KB | 1 | 8640 | 10,8 USD |
| Logowania | 1000 | 178 | 53400 | 106,8 MB | 418 | 3 611 520 | 11,06 USD |  


## <a name="frequently-asked-questions"></a>Często zadawane pytania

Ta sekcja zawiera często zadawane pytania i znane problemy z dziennikami usługi Azure Active Directory w usłudze Azure Monitor.

**Pytanie: Gdzie mam zacząć?** 

**Odpowiedź:** Rozpocznij od [Omówienia](reporting-azure-monitor-diagnostics-overview.md), aby uzyskać pojęcie o tym, co jest potrzebne do wdrożenia tej funkcji. Po zapoznaniu się z wymaganiami wstępnymi przejdź do samouczków, które pomogą Ci skonfigurować i skierować dzienniki do usługi Event Hubs.

---

**Pytanie: Które dzienniki są uwzględnione?**

**Odpowiedź:** Za pomocą tej funkcji można przekazywać zarówno dzienniki logowań, jak i dzienniki inspekcji, chociaż zdarzenia inspekcji związane z usługąB2C nie są obecnie uwzględniane. Zapoznaj się ze [schematem dziennika inspekcji](reporting-azure-monitor-diagnostics-audit-log-schema.md) i [schematem dziennika logowania](reporting-azure-monitor-diagnostics-sign-in-log-schema.md), aby dowiedzieć się, jakie typy dzienników i które oparte na funkcjach dzienniki są obecnie obsługiwane. 

---

**Pytanie: Jak szybko po wykonaniu akcji odpowiednie dzienniki pojawiają się w usłudze Event Hubs?**

**Odpowiedź:** Dzienniki powinny być widoczne w usłudze Event Hubs w dwie do pięciu minut po wykonaniu akcji. Aby uzyskać więcej informacji na temat usługi Event Hubs, zobacz [Co to jest usługa Event Hubs?](/azure/event-hubs/event-hubs-what-is-event-hubs.md)

---

**Pytanie: Jak szybko po wykonaniu akcji odpowiednie dzienniki pojawiają się na kontach magazynu?**

**Odpowiedź:** W przypadku kont usługi Azure Storage opóźnienie wynosi od 5 do 15 minut po wykonaniu akcji.

---

**Pytanie: Ile będzie kosztować przechowywanie danych?**

**Odpowiedź:** koszt przechowywania danych zależy od rozmiaru dzienników, jak również wybranego okresu przechowywania. Aby uzyskać przybliżone oszacowanie kosztów dla dzierżaw w zależności od rozmiaru wygenerowanych dzienników, sprawdź [Przegląd](reporting-azure-monitor-diagnostics-overview.md).

---

**Pytanie: Ile będzie kosztować przesyłanie strumieniowe danych do usługi Event Hubs?**

**Odpowiedź:** Koszt przesyłania strumieniowego zależy od liczby komunikatów odbieranych na minutę. Przeczytaj [Przegląd](reporting-azure-monitor-diagnostics-overview.md), aby dowiedzieć się więcej na temat sposobu obliczania kosztów i uzyskać przybliżone oszacowanie na podstawie liczby komunikatów. 

---

**Pytanie: Jakie narzędzia SIEM są obecnie obsługiwane?** 

**Odpowiedź:** Obecnie usługa Azure Monitor jest obsługiwana przez narzędzia Splunk, QRadar i Sumologic. Jednak Splunk jest jedynym narzędziem SIEM obsługiwanym w przypadku dzienników usługi Azure Active Directory. Aby uzyskać więcej informacji na temat sposobu działania łączników, zobacz [Stream Azure monitoring data to an event hub for consumption by an external tool](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs) (Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń do użycia przez zewnętrzne narzędzie).

---

**Pytanie: Czy można uzyskać dostęp do danych z centrum zdarzeń bez użycia zewnętrznego narzędzia SIEM?** 

**Odpowiedź:** Tak, możesz użyć [interfejsu API centrum zdarzeń](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph), aby uzyskać dostęp do dzienników z aplikacji niestandardowej. 

---


## <a name="next-steps"></a>Następne kroki

* [Archiwizowanie dzienników aktywności na koncie magazynu](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Kierowanie dzienników aktywności do centrum zdarzeń](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Dowiedz się więcej o dziennikach diagnostycznych platformy Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)