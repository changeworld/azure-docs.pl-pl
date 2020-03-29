---
title: Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory
description: Często zadawane pytania dotyczące ustawień roamingu w stanie przedsiębiorstwa na urządzeniach z systemem Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d6266f5ea8cdd4f75d0449cb49852e71c905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672399"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory

Roaming w stanie przedsiębiorstwa jest dostępny dla każdej organizacji z licencją Usługi Azure AD Premium lub Enterprise Mobility + Security (EMS). Aby uzyskać więcej informacji na temat sposobu uzyskania subskrypcji usługi Azure AD, zobacz [stronę produktu usługi Azure AD](https://azure.microsoft.com/services/active-directory).

Po włączeniu roamingu w stanie przedsiębiorstwa twoja organizacja automatycznie otrzymuje bezpłatną licencję o ograniczonym zastosowaniu dla ochrony usługi Azure Rights Management z usługi Azure Information Protection. Ta bezpłatna subskrypcja jest ograniczona do szyfrowania i odszyfrowywania ustawień przedsiębiorstwa i danych aplikacji synchronizowanych przez roaming stanu przedsiębiorstwa. Aby korzystać z pełnych możliwości usługi Azure Rights Management, musisz mieć [płatną subskrypcję.](https://azure.microsoft.com/pricing/details/information-protection/)

> [!NOTE]
> Ten artykuł dotyczy przeglądarki microsoft edge legacy w formacie HTML uruchomionej w systemie Windows 10 w lipcu 2015 r. Artykuł nie ma zastosowania do nowej przeglądarki opartej na chromie Microsoft Edge, wydanej 15 stycznia 2020 roku. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Aby włączyć roaming w stanie przedsiębiorstwa

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).
1. Wybierz pozycję Roaming w usłudze **Azure w usłudze Azure w** &gt; **usłudze** &gt; Azure Urządzenia z usługą Enterprise Devices w **trybie korporacyjnym**.
1. Wybierz **Użytkownicy mogą synchronizować ustawienia i dane aplikacji na różnych urządzeniach**. Aby uzyskać więcej informacji, zobacz [jak skonfigurować ustawienia urządzenia](/azure/active-directory/device-management-azure-portal).
  
   ![obraz ustawienia urządzenia oznaczone Użytkownicy mogą synchronizować ustawienia i dane aplikacji na różnych urządzeniach](./media/enterprise-state-roaming-enable/device-settings.png)
  
Aby urządzenie z systemem Windows 10 korzystało z usługi roamingu stanu przedsiębiorstwa, urządzenie musi uwierzytelniać się przy użyciu tożsamości usługi Azure AD. W przypadku urządzeń, które są przyłączone do usługi Azure AD, podstawową tożsamością logowania użytkownika jest ich tożsamość usługi Azure AD, więc nie jest wymagana żadna dodatkowa konfiguracja. W przypadku urządzeń korzystających z lokalnej usługi Active Directory administrator IT musi [skonfigurować urządzenia połączone z hybrydową usługą Azure Active Directory.](hybrid-azuread-join-manual-steps.md) 

## <a name="data-storage"></a>Magazyn danych

Dane roamingu stanu przedsiębiorstwa są hostowane w co najmniej jednym [regionie platformy Azure,](https://azure.microsoft.com/regions/) które najlepiej pasują do wartości kraju/regionu ustawionej w wystąpieniu usługi Azure Active Directory. Dane w roamingu stanu przedsiębiorstwa są podzielone na partycje na podstawie trzech głównych regionów geograficznych: Ameryki Północnej, EMEA i APAC. Dane roamingu stanu przedsiębiorstwa dla dzierżawcy znajdują się lokalnie z regionem geograficznym i nie są replikowane w różnych regionach.  Przykład:

| Wartość kraju/regionu | hostuje swoje dane w |
| -------------------- | ------------------------ |
| Kraj/region EMEA, taki jak Francja czy Zambia | Co najmniej jeden z regionów platformy Azure w Europie |
| Kraj/region Ameryki Północnej, taki jak Stany Zjednoczone lub Kanada | Co najmniej jeden z regionów platformy Azure w Stanach Zjednoczonych |
| Kraj/region APAC, taki jak Australia lub Nowa Zelandia | Co najmniej jeden region platformy Azure w Azji |
| Regiony Ameryki Południowej i Antarktydy | Co najmniej jeden region platformy Azure w Stanach Zjednoczonych |

Wartość kraju/regionu jest ustawiana jako część procesu tworzenia katalogu usługi Azure AD i nie można jej następnie zmodyfikować. Jeśli potrzebujesz więcej informacji na temat lokalizacji magazynu danych, złóż bilet za pomocą [pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/options/)

## <a name="view-per-user-device-sync-status"></a>Wyświetlanie stanu synchronizacji na urządzenie użytkownika

Wykonaj następujące kroki, aby wyświetlić raport o stanie synchronizacji na urządzenie dla użytkownika.

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).
1. Wybierz **pozycję Użytkownicy** &gt; **usługi Azure Active Directory** &gt; **Wszyscy użytkownicy**.
1. Wybierz użytkownika, a następnie wybierz pozycję **Urządzenia**.
1. W obszarze **Pokaż**wybierz **pozycję Ustawienia synchronizacji urządzeń i dane aplikacji,** aby wyświetlić stan synchronizacji.
  
   ![obraz ustawienia danych synchronizacji urządzenia](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Jeśli istnieją urządzenia synchronizowane dla tego użytkownika, zobaczysz urządzenia, jak pokazano tutaj.
  
   ![obraz danych kolumnowych synchronizacji urządzenia](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Przechowywanie danych

Dane synchronizowane z chmurą firmy Microsoft przy użyciu języka Enterprise State Roaming są przechowywane do czasu ich ręcznego usunięcia lub do momentu, gdy dane zostaną uznane za przestarzałe. 

### <a name="explicit-deletion"></a>Jawne usunięcie

Jawne usunięcie jest wtedy, gdy administrator platformy Azure usuwa użytkownika lub katalogu lub w inny sposób żąda jawnie, że dane mają zostać usunięte.

* **Usunięcie użytkownika:** Gdy użytkownik zostanie usunięty w usłudze Azure AD, dane mobilne konta użytkownika są usuwane po 90 do 180 dniach. 
* **Usunięcie katalogu:** Usunięcie całego katalogu w usłudze Azure AD jest natychmiastową operacją. Wszystkie dane ustawień skojarzone z tym katalogiem są usuwane po 90 do 180 dniach. 
* **Po usunięciu żądania:** Jeśli administrator usługi Azure AD chce ręcznie usunąć dane lub dane ustawień określonego użytkownika, administrator może zgłosić bilet za pomocą [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Nieaktualne usuwanie danych

Dane, które nie były dostępne przez jeden rok ("okres przechowywania") będą traktowane jako przestarzałe i mogą zostać usunięte z chmury firmy Microsoft. Okres przechowywania może ulec zmianie, ale nie będzie krótszy niż 90 dni. Nieaktualne dane mogą być określonym zestawem ustawień systemu Windows/aplikacji lub wszystkimi ustawieniami dla użytkownika. Przykład:

* Jeśli żadne urządzenia nie uzyskują dostępu do określonej kolekcji ustawień (na przykład aplikacja jest usuwana z urządzenia lub grupa ustawień, taka jak "Motyw" jest wyłączona dla wszystkich urządzeń użytkownika), kolekcja ta staje się przestarzała po upływie okresu przechowywania i może zostać usunięta . 
* Jeśli użytkownik wyłączy synchronizację ustawień na wszystkich swoich urządzeniach, żadne z danych ustawień nie będzie dostępne, a wszystkie dane ustawień dla tego użytkownika staną się przestarzałe i mogą zostać usunięte po upływie okresu przechowywania. 
* Jeśli administrator katalogu usługi Azure AD wyłączy roaming stanu przedsiębiorstwa dla całego katalogu, wszyscy użytkownicy w tym katalogu przestaną synchronizować ustawienia, a wszystkie dane ustawień dla wszystkich użytkowników staną się przestarzałe i mogą zostać usunięte po upływie okresu przechowywania. 

### <a name="deleted-data-recovery"></a>Odzyskiwanie usuniętych danych

Zasady przechowywania danych nie można skonfigurować. Po trwałym usunięciu danych nie można ich odzyskać. Jednak dane ustawień są usuwane tylko z chmury firmy Microsoft, a nie z urządzenia użytkownika końcowego. Jeśli którekolwiek urządzenie później ponownie połączy się z usługą roamingu stanu przedsiębiorstwa, ustawienia są ponownie synchronizowane i przechowywane w chmurze firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* [Omówienie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-overview.md)
* [Roaming ustawień i danych — często zadawane pytania](enterprise-state-roaming-faqs.md)
* [Ustawienia zasad grupy i mdm synchronizacji ustawień](enterprise-state-roaming-group-policy-settings.md)
* [Informacje dotyczące ustawień roamingu w systemie Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Rozwiązywanie problemów](enterprise-state-roaming-troubleshooting.md)
