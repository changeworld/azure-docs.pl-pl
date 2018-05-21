---
title: Włącz Roaming stanu przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące ustawień roamingu stanu przedsiębiorstwa na urządzeniach z systemem Windows. Roamingu stanu przedsiębiorstwa udostępnia użytkownikom środowisko unified na ich urządzeniach z systemem Windows i skraca czas potrzebny na konfigurowanie nowego urządzenia.
services: active-directory
keywords: roaming, chmury systemu windows, jak włączyć roamingu stanu przedsiębiorstwa stanu przedsiębiorstwa
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.openlocfilehash: dba749b6d85898e6438ce1160b9bf6eaff6f4ac9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory
Roamingu stanu przedsiębiorstwa jest dostępne dla każdej organizacji z usługi Azure AD Premium lub pakietu Enterprise Mobility + Security (EMS) licencji. Aby uzyskać więcej informacji na temat sposobu uzyskania subskrypcji usługi Azure AD, zobacz [stronę produktu usługi Azure AD](https://azure.microsoft.com/services/active-directory).

Po włączeniu roamingu stanu przedsiębiorstwa organizacji automatycznie otrzymuje licencję bezpłatne, ograniczonej ochrony usługi Azure Rights Management z usługi Azure Information Protection. Bezpłatna subskrypcja jest ograniczona do szyfrowania i odszyfrowywania ustawienia przedsiębiorstwa i dane aplikacji synchronizowane przez roamingu stanu przedsiębiorstwa. Musi mieć [płatną subskrypcję](https://azure.microsoft.com/pricing/details/information-protection/) do używania w pełni możliwości usługi Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Aby włączyć roamingu stanu przedsiębiorstwa

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).

2. Wybierz **usługi Azure Active Directory** &gt; **urządzeń** &gt; **ustawienia urządzenia**.

3. Wybierz **użytkownicy mogą synchronizować ustawienia i dane aplikacji na urządzeniach**. Aby uzyskać więcej informacji, zobacz [jak skonfigurować ustawienia urządzenia](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Obraz ustawienie urządzenia użytkowników mogą synchronizacji ustawień i danych aplikacji na urządzeniach](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Dla urządzeń z systemem Windows 10 do korzystania z roamingu stanu przedsiębiorstwa usługi urządzenie musi uwierzytelnić przy użyciu tożsamości usługi Azure AD. W przypadku urządzeń, które dołączyły do usługi Azure AD głównej logowania tożsamości użytkownika jest swojej tożsamości usługi Azure AD, więc dodatkowa konfiguracja nie jest wymagane. W przypadku urządzeń korzystających z lokalnej usługi Active Directory, administrator IT musi [łączenie urządzeń przyłączonych do domeny do usługi Azure AD dla systemu Windows 10 napotyka](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Magazyn danych
Roamingu stanu przedsiębiorstwa danych znajduje się w co najmniej jednej [regiony platformy Azure](https://azure.microsoft.com/regions/) że najlepiej są wyrównane o wartości kraj/region w wystąpieniu usługi Azure Active Directory. Dane roamingu stanu przedsiębiorstwa jest podzielona na partycje oparte na trzech głównych regionach geograficznych: Ameryce Północnej, regionie oraz APAC. Roamingu stanu przedsiębiorstwa danych dla dzierżawy znajduje się lokalnie z region geograficzny, a nie są replikowane w regionach.  Na przykład:
Wartość kraj/region | swoje dane hosted w
---------------------|-------------------------
EMEA kraju, takich jak "Francja" lub "Zambii" | co najmniej regionów platformy Azure w ramach Europy 
(Ameryka Północna) kraju, takich jak "Stanów Zjednoczonych" lub "Kanada" | co najmniej jeden regiony platformy Azure w Stanach Zjednoczonych
APAC kraju, takich jak "Australia" lub "Nowa Zelandia" | co najmniej jeden regiony platformy Azure w ramach Azja
American Południowe i Antarktyka regionów | co najmniej jeden regiony platformy Azure w Stanach Zjednoczonych

Wartość kraju/regionu jest ustawiony jako część procesu tworzenia katalogu usługi Azure AD i nie można zmodyfikować później. Jeśli potrzebujesz więcej szczegółowych informacji o Twojej lokalizacji magazynu danych, założyć zgłoszenie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Wyświetl stan synchronizacji urządzeń na użytkownika
Wykonaj następujące kroki, aby wyświetlić raport o stanie synchronizacji urządzeń na użytkownika.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).

2. Wybierz **usługi Azure Active Directory** &gt; **użytkowników i grup** &gt; **wszyscy użytkownicy**.

3. Wybierz użytkownika, a następnie wybierz **urządzeń**.

4. W obszarze **Pokaż**, wybierz pozycję **urządzeń Synchronizowanie ustawień i danych aplikacji** do określenia stanu synchronizacji.
  
  ![Obraz ustawień danych synchronizacji urządzeń](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. W przypadku urządzeń synchronizacji dla tego użytkownika, zobacz urządzeń w sposób pokazany poniżej.
  
  ![Obraz danych kolumnowy synchronizacji urządzenia](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Przechowywanie danych
Synchronizowane z platformy Azure przy użyciu roamingu stanu przedsiębiorstwa danych jest zachowywana do momentu został ręcznie usunięty lub w danych zostanie uznane za przestarzałe. 

### <a name="explicit-deletion"></a>Jawne usuwanie
Jawne usuwanie jest, gdy administrator Azure w przeciwnym razie żąda je jawnie danych ma zostać usunięty lub usunięcie użytkownika lub katalogu.

* **Usunięcie użytkownika**: gdy użytkownik zostanie usunięty w usłudze Azure AD, konto użytkownika, roaming danych zostanie usunięte po 90 na 180 dni. 
* **Usuwanie katalogu**: usunięcie całego katalogu w usłudze Azure AD jest natychmiastowego działania. Wszystkie dane ustawienia skojarzone z których katalogu jest usuwane po 90 na 180 dni. 
* **Na żądanie usunięcia**: Jeśli administrator usługi Azure AD chce ręcznie usunąć dane i ustawienia danych określonego użytkownika, administrator może założyć zgłoszenie [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Usuwanie starych danych
Dane, które nie była używana przez jeden rok ("okres przechowywania") będzie traktowana jako przestarzały i mogą zostać usunięte z platformy Azure. Okres przechowywania może ulec zmianie, ale nie będzie mniejsza niż 90 dni. Stare dane mogą być określonych aplikacji systemu Windows/ustawienia lub wszystkich ustawień dla użytkownika. Na przykład:

* Jeśli kolekcja określone ustawienia dostępu do żadnych urządzeń (na przykład, aplikacja zostanie usunięty z urządzenia lub grupy ustawień, takich jak "Motywu" jest wyłączona dla wszystkich urządzeń użytkownika), a następnie tej kolekcji jest przestarzała po upływie okresu przechowywania i mogą zostać usunięte . 
* Jeśli użytkownik wyłączył ustawienia synchronizacji na wszystkich swoich urządzeniach, następnie żadne dane ustawienia jest niedostępny i wszystkich danych ustawień dla tego użytkownika zostanie nieodświeżone i mogą zostać usunięte po upływie okresu przechowywania. 
* Jeśli administrator katalogu usługi Azure AD wyłącza roamingu stanu przedsiębiorstwa dla całego katalogu, a następnie wszyscy użytkownicy w tym katalogu zostanie zatrzymana, ustawienia synchronizacji, a wszystkie dane ustawienia dla wszystkich użytkowników będzie nieodświeżone i mogą zostać usunięte po upływie okresu przechowywania. 

### <a name="deleted-data-recovery"></a>Odzyskiwanie usuniętego danych
Nie można skonfigurować zasady przechowywania danych. Gdy dane są trwale usuwane, nie jest możliwe do odzyskania. Jednak dane ustawienia są usuwane tylko z platformy Azure, nie z urządzenia przez użytkownika końcowego. Dowolne urządzenie później połączenie z usługą roamingu stanu przedsiębiorstwa, ustawienia są ponownie zsynchronizowane i przechowywane na platformie Azure.

## <a name="related-topics"></a>Powiązane tematy
* [Opis roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-overview.md)
* [Ustawienia i dane mobilne — często zadawane pytania](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Ustawienia zasad i zarządzania urządzeniami Przenośnymi dla ustawień synchronizacji grupy](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Informacje dotyczące ustawień roamingu systemu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Rozwiązywanie problemów](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
