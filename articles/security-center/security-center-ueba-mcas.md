---
title: Wykrywanie w usłudze Azure Security Center zagrożeń | Dokumentacja firmy Microsoft
description: " Wykrywanie zagrożeń i złośliwych dzięki integracji Microsoft Cloud App Security z usługą Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: af7896ec4afaeefda7261542bf593a89a7bb9ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551799"
---
# <a name="ueba-for-azure-resources-and-users"></a>Analiza behawioralna użytkowników i jednostek (UEBA) dla zasobów i użytkowników platformy Azure 

Usługa Azure Security Center partnerów firmy Microsoft Cloud App Security, aby zapewnić Ci alerty oparte na użytkownika i analizie zachowań jednostek (UEBA) dla zasobów platformy Azure i użytkowników (aktywności platformy Azure). Te alerty wykrywają nieprawidłowości w zachowaniu użytkowników i są oparte na analizie behawioralnej użytkowników i jednostek oraz uczeniu maszynowym (ML, machine learning), dzięki czemu można natychmiast uruchomić zaawansowane wykrywanie zagrożeń wśród działań wykonywanych na Twoich subskrypcjach. Ponieważ są one automatycznie włączone, nowe wykrycia anomalii dostarczają natychmiastowe wyniki, zapewniając błyskawiczne wykrywanie, ukierunkowując się na liczne anomalie w zachowaniu użytkowników i zasobów powiązanych z subskrypcją. Ponadto te alerty korzystają z dodatkowych danych, które już istnieją w aparacie wykrywania Microsoft Cloud App Security w celu przyspieszenia procesu badania i powstrzymywania bieżących zagrożeń. 

> [!NOTE]
> Usługi Azure Security Center, która może przechowywać kopię danych klientów związanych z zabezpieczeniami, zebranych z lub skojarzone z zasobem klientów (np. maszyna wirtualna lub dzierżawy usługi Azure Active Directory): () w tej samej lokalizacji geograficznej co tego zasobu, z wyjątkiem w tych obszarach geograficznych gdzie firmy Microsoft nie ma jeszcze do wdrożenia usługi Azure Security Center, w którym będą przechowywane w przypadku kopii tych danych w Stanach Zjednoczonych. i (b) w przypadku, gdy usługa Azure Security Center używa innej usługi Online firmy Microsoft do przetwarzania tych danych, mogą być przechowywane takich danych zgodnie z regułami geolokalizacja tej usługi Online.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Prawidłowy, aktywować [licencji Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [Usługa Security Center w warstwie standardowa](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Alerty wykrywania zagrożeń

Usługa Security Center obsługuje alerty wykrywania anomalii w usłudze Cloud App Security, takie jak:

**Niemożliwa podróż**
-  Wykrywanie identyfikuje dwa działania użytkownika (jest jedną lub wiele sesji) pochodzące z komputera odległym geograficznie lokalizacji w przedziale czasu krótszy niż czas jego będą miały użytkownikowi przesyłane z pierwszej lokalizacji do drugiego, wskazując czy inny użytkownik korzysta z tych samych poświadczeń. Wykrywanie korzysta z usługi machine learning algorytmu, który ignoruje oczywiste "wyniki fałszywie dodatnie" Współtworzenie warunek niemożliwych podróży, takich jak sieci VPN i lokalizacje, które regularnie używane przez innych użytkowników w organizacji. Wykrywanie ma wstępny okres uczenia wynoszący 7 dni, podczas których uczy się wzorca działania nowego użytkownika.

**Aktywność z rzadko występującego kraju**
- Wykrywanie uwzględnia ostatnie lokalizacje działań do określenia lokalizacji nowych i rzadkie. Aparatu wykrywania anomalii są przechowywane informacje o powyższych lokalizacjach, które posługują się użytkownicy w organizacji. Alert jest wyzwalany, gdy działanie odbywa się z lokalizacji, która nie był nigdy lub ostatnio odwiedzany przez dowolnego użytkownika w organizacji. 

**Aktywność z anonimowych adresów IP**
- Wykrywanie Określa, że użytkownicy był aktywny z adresu IP, który został zidentyfikowany jako adres IP anonimowego serwera proxy. Te serwery proxy są wykorzystywane przez osoby, które chcą ukryć adres IP swojego urządzenia i mogą być używane do złośliwego działania. Wykrywanie korzysta z usługi machine learning algorytmu, który ogranicza "wyniki fałszywie dodatnie", takie jak źle oznakowane adresy IP są powszechnie używane przez użytkowników w organizacji.
 
  ![Alert wykrycia zagrożeń](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Wyłączanie alerty wykrywania zagrożeń

Te alerty są domyślnie włączone, ale można je wyłączyć:

1. W bloku usługi Security Center wybierz **ceny u & stawienia** i wybierz subskrypcję, zastosowanie.
2. Kliknij przycisk **wykrywanie zagrożeń**.
3. W obszarze **korzystanie z integracji**, usuń zaznaczenie pola wyboru **Zezwalaj Microsoft Cloud App Security na dostęp do moich danych**i kliknij przycisk **Zapisz**.

   ![Alert wykrycia zagrożeń](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Brak wstępny okres uczenia wynoszący 7 dni, podczas których anomalii nie wszystkie alerty związane z wykrywaniem są wywoływane. Po zakończeniu szkolenia każda sesja jest porównywana z działaniami z ostatniego miesiąca (pod kątem m.in. okresu aktywności użytkowników oraz wykrytych adresów IP i urządzeń) z uwzględnieniem oceny ryzyka tych działań. Wykrywane odmiany są częścią maszyny uczenia aparatu wykrywania anomalii tego profile Twojego środowiska i wyzwalania alertów w odniesieniu do linii bazowej, która jest uzyskiwana na działanie Twojej organizacji. Wykrywane odmiany także korzystać z algorytmów uczenia maszynowego umożliwiający profile użytkowników i wzorzec logowania, aby zmniejszyć liczbę fałszywych alarmów.
>
  
## <a name="next-steps"></a>Kolejne kroki
W tym artykule pokazano, do pracy z wykrywania zagrożeń w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
