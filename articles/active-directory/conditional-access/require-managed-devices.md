---
title: 'Jak: Wymagaj zarządzanego urządzenia do uzyskania dostępu do aplikacji w chmurze przy użyciu usługi Azure Active Directory dostępu warunkowego | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory (Azure AD) opartego na urządzeniach zasady dostępu warunkowego, które wymagają zarządzanych urządzeń, aby uzyskać dostęp do aplikacji w chmurze.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9c99b8390cd43c3f0767123684fe06e0ae74f86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509362"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Instrukcje: Wymagaj urządzenia zarządzanego do uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego

W świecie urządzeń przenośnych i chmurze — Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do aplikacji i usług z dowolnego miejsca. Autoryzowani użytkownicy będą mogli swoje aplikacje w chmurze z szerokiej gamy urządzeń w tym komórkowego, a także urządzeniami osobistymi. Jednak w wielu środowiskach ma co najmniej kilka aplikacji, które powinny zostać oceniony jedynie przez urządzenia, które spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. Urządzenia te są nazywane także zarządzanych urządzeń. 

W tym artykule opisano sposób konfigurowania zasad dostępu warunkowego, które wymagają zarządzanych urządzeń, aby dostęp do niektórych aplikacji w chmurze w swoim środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

Wymaganie zarządzanych urządzeń do chmury aplikacji dostęp do powiązania **dostępu warunkowego usługi Azure AD** i **zarządzania urządzeniami usługi Azure AD** ze sobą. Jeśli nie jesteś zaznajomiony z jednego z tych obszarów jeszcze, najpierw należy przeczytać następujące tematy:

- **[Dostęp warunkowy w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md)**  — ten artykuł zawiera omówienie pojęć dotyczących dostępu warunkowego i powiązane terminologii.
- **[Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../devices/overview.md)**  — ten artykuł zawiera omówienie różnych opcji, musisz zakupić urządzenia pod kontrolą organizacji. 

## <a name="scenario-description"></a>Opis scenariusza

Doskonalenie znajomości równowagę między zabezpieczenia i wydajność jest trudne. Rozprzestrzenianie obsługiwanych urządzeń, dostęp do zasobów chmury pozwala zwiększyć produktywność użytkowników. Na drugiej strony prawdopodobnie nie ma niektórych zasobów w danym środowisku, aby były dostępne dla urządzeń z poziomu ochrony nieznany. Dla zasobów, których dotyczy problem należy wymagać, czy użytkownicy mogą dostęp tylko do ich za pomocą urządzenia zarządzanego. 

Przy użyciu dostępu warunkowego usługi Azure AD można rozwiązać tego wymagania, z jedną zasadę, która udziela dostępu:

- Wybrana chmura usługi aplikacji
- Dla wybranych użytkowników i grup
- Wymaganie urządzenia zarządzanego

## <a name="managed-devices"></a>Urządzenia zarządzane  

W prostych słowach zarządzanych urządzeń są urządzenia, które są objęte *jakieś* formantu organizacji. W usłudze Azure AD wymagań wstępnych dotyczących zarządzanego urządzenia jest, że ma on zarejestrowany za pomocą usługi Azure AD. Zarejestruj urządzenie tworzy tożsamość urządzenia w postaci obiektu urządzenia. Ten obiekt jest używany przez platformę Azure do śledzenia stanu informacji o urządzeniu. Jako administrator usługi Azure AD można już ten obiekt jest używany do przełączania (Włącz/Wyłącz) stan urządzenia.
  
![Warunków opartych na urządzeniu](./media/require-managed-devices/32.png)

Aby uzyskać urządzeń zarejestrowanych w usłudze Azure AD, masz trzy opcje: 

- **Urządzeń zarejestrowanych w usłudze Azure AD** — osobiste urządzenie zarejestrowane w usłudze Azure AD
- **Urządzenia przyłączone do usługi Azure AD** — Aby uzyskać organizacji urządzenia systemu Windows 10, który nie jest dołączony do sieci lokalnej usługi AD zarejestrowane w usłudze Azure AD. 
- **Urządzenia przyłączone do hybrydowej usługi Azure AD** — Pobierz system Windows 10 lub obsługiwanego urządzenia niskiego poziomu, której jest dołączony do sieci lokalnej usługi AD zarejestrowane w usłudze Azure AD.

Te trzy opcje zostały omówione w artykule [co to jest tożsamość urządzenia?](../devices/overview.md)

Aby stać się urządzenia zarządzanego, zarejestrowanego urządzenia musi być albo **urządzenia połączonego z hybrydowej usługi Azure AD** lub **urządzenia, która została oznaczona jako zgodna**.  

![Warunków opartych na urządzeniu](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Wymagaj hybrydowych usługi Azure AD urządzenia przyłączone do

Zasady dostępu warunkowego, można wybrać **Wymagaj urządzenia dołączonego do hybrydowej usługi Azure AD** do stanu, że aplikacji w wybranej chmurze dostęp jest możliwy tylko za pomocą urządzenia zarządzanego. 

![Warunków opartych na urządzeniu](./media/require-managed-devices/10.png)

To ustawienie dotyczy tylko systemu Windows 10 lub urządzenia niskiego poziomu, takiego jak Windows 7 czy Windows 8, które są przyłączone do lokalnej usługi AD. Można zarejestrować tylko te urządzenia z usługą Azure AD przy użyciu dołączenie do hybrydowej usługi Azure AD, która jest [zautomatyzowanego procesu](../devices/hybrid-azuread-join-plan.md) można pobrać urządzenia z systemem Windows 10 zarejestrowane. 

![Warunków opartych na urządzeniu](./media/require-managed-devices/45.png)

Co sprawia, że hybrydowej usługi Azure AD urządzenia połączonego z zarządzanego urządzenia?  W przypadku urządzeń, które są przyłączone do lokalnej usługi AD, zakłada się, że kontrolę nad tymi urządzeniami jest wymuszana przy użyciu rozwiązania do zarządzania, takich jak **programu System Center Configuration Manager (SCCM)** lub **zasad (zasad grupy)grupy** do zarządzania nimi. Ponieważ nie istnieje metoda dla usługi Azure AD w celu określenia, czy każda z tych metod zostało zastosowane do urządzenia, wymaganie urządzenia przyłączone do hybrydowej usługi Azure AD jest stosunkowo słaba mechanizm wymagają zarządzanego urządzenia. Jest on do Ciebie jako administrator, aby ocenić, czy metod, które są stosowane do lokalnej przyłączonych do domeny urządzenia są wystarczająco silne, stanowić zarządzanego urządzenia, jeśli takie urządzenia jest również urządzenia przyłączone do hybrydowej usługi Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Opcja *Wymagaj, aby urządzenie było oznaczone jako zgodne* jest najsilniejszą żądania urządzenia zarządzanego.

![Warunków opartych na urządzeniu](./media/require-managed-devices/11.png)

Ta opcja wymaga urządzenia zarejestrowane w usłudze Azure AD, a także można oznaczyć jako zgodnego ze przez:
         
- Usługa Intune.
- System zarządzania urządzeniami Przenośnymi urządzeniami przenośnymi innej firmy, który zarządza urządzeń z systemem Windows 10 za pomocą integracji z usługą Azure AD. Systemy zarządzania urządzeniami Przenośnymi innej firmy dla systemu operacyjnego urządzenia typów innych niż Windows 10 nie są obsługiwane.
 
![Warunków opartych na urządzeniu](./media/require-managed-devices/46.png)

Dla urządzenia, które jest oznaczone jako zgodne można założyć, że: 

- Zarządzania urządzeniami przenośnymi używanymi przez pracowników dostępu do danych firmy
- Są zarządzane aplikacje mobilne, używanymi przez pracowników
- Informacje o Twojej firmie jest chroniony przez kontrolowanie sposobu uzyskuje dostęp do Twoich pracowników i udostępnia ją
- Urządzenia i jego aplikacje są zgodne z wymaganiami firmy dotyczącymi bezpieczeństwa

## <a name="next-steps"></a>Kolejne kroki

Przed skonfigurowaniem zasad dostępu warunkowego opartego na urządzeniach w danym środowisku, powinno zająć się [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).
