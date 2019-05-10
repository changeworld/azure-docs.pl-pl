---
title: Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące ustawień roamingu stanu przedsiębiorstwa na urządzeniach Windows. Roaming stanu dla przedsiębiorstw udostępnia użytkownikom na ich urządzeniach Windows ujednoliconego środowiska i skraca czas potrzebny do konfigurowania nowego urządzenia.
services: active-directory
keywords: roaming, chmura systemu windows, jak włączyć roaming stanu dla przedsiębiorstw stanu dla przedsiębiorstw
documentationcenter: ''
author: tanning
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7e665f3124296392c8138f4f2bd3632b56e53de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230934"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory
Roaming stanu dla przedsiębiorstw jest dostępny dla każdej organizacji za pomocą usługi Azure AD Premium lub pakietu Enterprise Mobility + Security (EMS) licencji. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcję usługi Azure AD, zobacz [strona produktu usługi Azure AD](https://azure.microsoft.com/services/active-directory).

Po włączeniu roamingu stanu przedsiębiorstwa organizacji jest automatycznie przyznawane bezpłatnej, ograniczonej licencji dla ochrony usługi Azure Rights Management z usługi Azure Information Protection. Bezpłatna subskrypcja jest ograniczona do szyfrowania i odszyfrowywania enterprise ustawień i danych aplikacji zsynchronizowane Roaming stanu dla przedsiębiorstw. Konieczne jest posiadanie [płatną subskrypcję](https://azure.microsoft.com/pricing/details/information-protection/) używać pełnego zestawu funkcji usługi Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Aby włączyć Roaming stanu dla przedsiębiorstw

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).

1. Wybierz **usługi Azure Active Directory** &gt; **urządzeń** &gt; **Roaming stanu przedsiębiorstwa**.

1. Wybierz **użytkownicy mogą synchronizować ustawień i danych aplikacji na urządzeniach**. Aby uzyskać więcej informacji, zobacz [jak skonfigurować ustawienia urządzenia](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![Obraz ustawień urządzeń z etykietą użytkownicy mogą synchronizować ustawień i danych aplikacji na urządzeniach](./media/enterprise-state-roaming-enable/device-settings.png)
  
Dla urządzeń systemu Windows 10 do korzystania z usługi Roaming stanu dla przedsiębiorstw urządzenie musi uwierzytelniać przy użyciu tożsamości usługi Azure AD. W przypadku urządzeń, które są przyłączone do usługi Azure AD podstawowego znak w tożsamość użytkownika jest swojej tożsamości usługi Azure AD, więc żadna dodatkowa konfiguracja jest wymagana. W przypadku urządzeń korzystających z usługi Active Directory w środowisku lokalnym, administrator IT musi [urządzenia przyłączone do hybrydowej Konfigurowanie usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual-steps). 

## <a name="data-storage"></a>Magazyn danych
Roaming stanu dla przedsiębiorstw danych znajduje się w jednej lub kilku [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) , najlepiej wyrównać kraju/regionu ma wartość w wystąpieniu usługi Azure Active Directory. Roaming stanu dla przedsiębiorstw danych jest podzielona na partycje oparte na trzech głównych regionów geograficznych: Ameryka Północna, EMEA i Azja i PACYFIK Roaming stanu dla przedsiębiorstw danych dla dzierżawy znajduje się lokalnie przy użyciu region geograficzny, a nie są replikowane między regionami.  Na przykład:

Wartość kraj/region | ma ich dane są obsługiwane w
---------------------|-------------------------
EMEA kraju/regionu, takich jak Francja "lub" Zambia | Co najmniej jeden z regionów platformy Azure w Europie 
Północnoamerykański kraju/regionu, takich jak Stany Zjednoczone i Kanada | co najmniej jeden z regionów platformy Azure na terenie Stanów Zjednoczonych
Azja i PACYFIK kraju/regionu, takich jak Australii lub Nowej Zelandii | co najmniej jeden z regionów platformy Azure w Azji
Regionach Ameryki Południowej i Antarktyda | co najmniej jeden region platformy Azure na terenie Stanów Zjednoczonych

Wartość kraju/regionu jest ustawiony jako część procesu tworzenia katalogu usługi Azure AD i nie można później zmodyfikować. Jeśli potrzebujesz więcej szczegółowych informacji o Twojej lokalizacji magazynu danych, Utwórz bilet [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Wyświetl stan synchronizacji urządzeń na użytkownika
Wykonaj następujące kroki, aby wyświetlić raport o stanie synchronizacji urządzeń na użytkownika.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).

1. Wybierz **usługi Azure Active Directory** &gt; **użytkowników** &gt; **wszyscy użytkownicy**.

1. Wybierz użytkownika, a następnie wybierz **urządzeń**.

1. W obszarze **Pokaż**, wybierz opcję **urządzenia synchronizujące ustawienia i dane aplikacji** do wyświetlania stanu synchronizacji.
  
   ![Obraz ustawień danych synchronizacji urządzeń](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. W przypadku urządzeń synchronizujących dla tego użytkownika, możesz zobaczyć urządzenia, jak pokazano poniżej.
  
   ![Obraz danych kolumnowych synchronizacji urządzeń](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Przechowywanie danych
Zsynchronizowane z chmurą Microsoft za pomocą Roaming stanu dla przedsiębiorstw dane są przechowywane do momentu został ręcznie usunięty lub danych w danym zostanie uznane za przestarzałe. 

### <a name="explicit-deletion"></a>Jawne usuwanie
Jawne usuwanie jest, gdy administrator platformy Azure spowoduje usunięcie użytkownika lub w katalogu lub w przeciwnym razie żądania je jawnie danych ma zostać usunięty.

* **Usuwanie użytkownika**: Po użytkownik zostanie usunięty w usłudze Azure AD, konto użytkownika, roaming danych jest usuwana po 90, 180 dni. 
* **Usuwanie katalogu**: Usuwanie cały katalog w usłudze Azure AD jest natychmiastowego działania. Wszystkie dane ustawienia związane z nią za pomocą których usługa zostanie wyłączona po upływie 90, 180 dni. 
* **Na żądanie usunięcia**: Jeśli administrator usługi Azure AD chce ręcznie usunąć dane lub ustawienia danych określonego użytkownika, administrator może bilet [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Usunięcie nieaktualnych danych
Dane, które nie była używana przez jeden rok ("okres przechowywania") będzie traktowane jako nieaktualne i mogą zostać usunięte z chmury firmy Microsoft. Okres przechowywania może ulec zmianie, ale nie będzie mniejszy niż 90 dni. Nieaktualnych danych może być określony zbiór ustawień Windows/aplikacji i wszystkich ustawień dla użytkownika. Na przykład:

* Jeśli żadne urządzenia dostępu do kolekcji określonego ustawienia (na przykład, aplikacja zostanie usunięty z urządzenia lub grupy ustawień, takich jak "Kompozycji" jest wyłączona dla wszystkich urządzeń użytkownika), a tej kolekcji staje się przestarzałe po upływie okresu przechowywania mogą zostać usunięte . 
* Jeśli użytkownik wyłączył ustawienia synchronizacji na wszystkich swoich urządzeniach, następnie żadne dane ustawienia będą uzyskiwać dostęp, a wszystkie dane ustawienia dla tego użytkownika staną się nieaktualne i mogą zostać usunięte po upływie okresu przechowywania. 
* Jeśli administrator usługi Azure AD directory wyłącza Roaming stanu dla przedsiębiorstw na cały katalog, a następnie wszyscy użytkownicy w tym katalogu zostanie zatrzymane, synchronizowanie ustawień, a wszystkie dane ustawienia dla wszystkich użytkowników staną się nieaktualne i mogą zostać usunięte po upływie okresu przechowywania. 

### <a name="deleted-data-recovery"></a>Odzyskiwanie usuniętych danych
Zasady przechowywania danych nie jest konfigurowany. Gdy dane zostaną trwale usunięte, nie jest możliwe do odzyskania. Jednak dane ustawienia są usuwane tylko w chmurze firmy Microsoft, nie z urządzenia przez użytkownika końcowego. Jeśli na dowolnym urządzeniu później ponownie nawiąże połączenie usługi Roaming stanu dla przedsiębiorstw, ustawienia są ponownie zsynchronizowane i przechowywane w chmurze firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-overview.md)
* [Często zadawane pytania dotyczące roaming ustawień i danych](enterprise-state-roaming-faqs.md)
* [Ustawienia zarządzania urządzeniami Przenośnymi i zasady w celu ustawienia synchronizacji grupy](enterprise-state-roaming-group-policy-settings.md)
* [Informacje dotyczące ustawień roamingu systemu Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Rozwiązywanie problemów](enterprise-state-roaming-troubleshooting.md)
