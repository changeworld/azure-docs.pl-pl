---
title: Dostęp warunkowy wymaga zarządzanego urządzenia — usługa Azure Active Directory
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego usługi Azure Active Directory (Azure AD) oparte na urządzeniach, które wymagają zarządzanych urządzeń do uzyskiwania dostępu do aplikacji w chmurze.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481487"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Jak: Wymaganie zarządzanych urządzeń do dostępu do aplikacji w chmurze za pomocą dostępu warunkowego

W świecie usługi Azure Active Directory (Azure AD) na urządzeniach mobilnych i chmurze umożliwia logowanie jednokrotne w aplikacjach i usługach z dowolnego miejsca. Autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze z szerokiej gamy urządzeń, w tym urządzeń mobilnych, a także osobistych. Jednak wiele środowisk ma co najmniej kilka aplikacji, do których powinny być dostępne tylko urządzenia spełniające standardy zabezpieczeń i zgodności. Urządzenia te są również nazywane urządzeniami zarządzanymi. 

W tym artykule wyjaśniono, jak skonfigurować zasady dostępu warunkowego, które wymagają zarządzanych urządzeń, aby uzyskać dostęp do niektórych aplikacji w chmurze w danym środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

Wymaganie zarządzanych urządzeń dostępu do aplikacji w chmurze łączy **usługi Azure AD Conditional Access** i zarządzanie **urządzeniami usługi Azure AD** razem. Jeśli nie znasz jeszcze jednego z tych obszarów, przeczytaj najpierw następujące tematy:

- **[Dostęp warunkowy w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** — w tym artykule przedstawiono omówienie koncepcyjne dostępu warunkowego i powiązanej terminologii.
- **[Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../devices/overview.md)** — w tym artykule przedstawiono omówienie różnych opcji, które należy uzyskać urządzenia pod kontrolą organizacji. 
- Aby uzyskać pomoc techniczną chrome w **systemie Windows 10 Creators Update (wersja 1703)** lub nowsza, zainstaluj [rozszerzenie Konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). To rozszerzenie jest wymagane, gdy zasady dostępu warunkowego wymaga szczegółów określonych urządzenia.

>[!NOTE] 
> Zalecamy korzystanie z zasad dostępu warunkowego opartego na urządzeniach usługi Azure AD, aby uzyskać najlepsze wymuszanie po początkowym uwierzytelnianiu urządzenia. Obejmuje to zamykanie sesji, jeśli urządzenie wypadnie z zgodności i przepływu kodu urządzenia.


## <a name="scenario-description"></a>Opis scenariusza

Opanowanie równowagi między bezpieczeństwem a wydajnością jest wyzwaniem. Rozprzestrzenianie się obsługiwanych urządzeń w celu uzyskania dostępu do zasobów w chmurze pomaga zwiększyć produktywność użytkowników. Z drugiej strony prawdopodobnie nie chcesz, aby niektóre zasoby w danym środowisku były dostępne dla urządzeń o nieznanym poziomie ochrony. W przypadku zasobów, których dotyczy problem, należy wymagać, aby użytkownicy mogli uzyskiwać do nich dostęp tylko przy użyciu urządzenia zarządzanego. 

Za pomocą usługi Azure AD dostęp warunkowy, można rozwiązać to wymaganie za pomocą jednej zasady, która udziela dostępu:

- Do wybranych aplikacji w chmurze
- Dla wybranych użytkowników i grup
- Wymaganie urządzenia zarządzanego

## <a name="managed-devices"></a>Urządzenia zarządzane  

W prostych słowach, zarządzane urządzenia są urządzeniami, które są pod *jakąś* kontrolą organizacyjną. W usłudze Azure AD warunkiem wstępnym dla zarządzanego urządzenia jest zarejestrowana w usłudze Azure AD. Rejestrowanie urządzenia tworzy tożsamość urządzenia w postaci obiektu urządzenia. Ten obiekt jest używany przez platformę Azure do śledzenia informacji o stanie urządzenia. Jako administrator usługi Azure AD możesz już użyć tego obiektu do przełączania (włączania/wyłączania) stanu urządzenia.
  
![Warunki oparte na urządzeniu](./media/require-managed-devices/32.png)

Aby uzyskać urządzenie zarejestrowane w usłudze Azure AD, masz trzy opcje: 

- **Urządzenia zarejestrowane w usłudze Azure AD** — aby uzyskać urządzenie osobiste zarejestrowane w usłudze Azure AD
- **Urządzenia przyłączone do usługi Azure AD** — aby uzyskać organizacyjne urządzenie z systemem Windows 10, które nie jest przyłączone do lokalnej usługi AD zarejestrowanej w usłudze Azure AD. 
- **Hybrydowe urządzenia przyłączone do usługi Azure AD** — aby uzyskać system Windows 10 lub obsługiwane urządzenie w dół, które jest przyłączone do lokalnej usługi AD zarejestrowanej w usłudze Azure AD.

Te trzy opcje zostały omówione w artykule [Co to jest tożsamość urządzenia?](../devices/overview.md)

Aby stać się urządzeniem zarządzanym, zarejestrowanym urządzeniem musi być **urządzenie przyłączone do usługi Hybrid Azure AD** lub **urządzenie, które zostało oznaczone jako zgodne.**  

![Warunki oparte na urządzeniu](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Wymagaj urządzeń przyłączonych do usługi Azure AD

W zasadach dostępu warunkowego można wybrać **opcję Wymagaj hybrydowego urządzenia przyłączonego do usługi Azure AD,** aby stwierdzić, że wybrane aplikacje w chmurze mogą być dostępne tylko przy użyciu urządzenia zarządzanego. 

![Warunki oparte na urządzeniu](./media/require-managed-devices/10.png)

To ustawienie dotyczy tylko urządzeń z systemem Windows 10 lub down-level, takich jak Windows 7 lub Windows 8, które są połączone z lokalną usługą AD. Te urządzenia można zarejestrować tylko w usłudze Azure AD przy użyciu hybrydowego sprzężenia usługi AZURE AD, które jest [zautomatyzowanym procesem](../devices/hybrid-azuread-join-plan.md) rejestracji urządzenia z systemem Windows 10. 

![Warunki oparte na urządzeniu](./media/require-managed-devices/45.png)

Co sprawia, że urządzenie przyłączone do usługi Azure AD hybrydowe jest urządzeniem zarządzanym?  W przypadku urządzeń, które są przyłączone do lokalnej usługi AD, zakłada się, że kontrola nad tymi urządzeniami jest wymuszana przy użyciu rozwiązań do zarządzania, takich jak **program Menedżer konfiguracji** lub zasady grupy **(GP),** aby nimi zarządzać. Ponieważ nie istnieje żadna metoda usługi Azure AD, aby ustalić, czy którakolwiek z tych metod została zastosowana do urządzenia, wymaganie hybrydowego urządzenia przyłączone do usługi Azure AD jest stosunkowo słabym mechanizmem wymagającym zarządzanego urządzenia. To do Ciebie jako administratora, aby ocenić, czy metody, które są stosowane do lokalnych urządzeń przyłączonych do domeny są wystarczająco silne, aby stanowić urządzenie zarządzane, jeśli takie urządzenie jest również hybrydowe urządzenie przyłączone do usługi Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Opcja *wymaga oznaczenia urządzenia jako zgodnego* jest najsilniejszym formularzem żądania zarządzanego urządzenia.

![Warunki oparte na urządzeniu](./media/require-managed-devices/11.png)

Ta opcja wymaga, aby urządzenie było zarejestrowane w usłudze Azure AD, a także oznaczone jako zgodne przez:
         
- Usługa Intune
- System zarządzania urządzeniami przenośnymi (MDM) innej firmy, który zarządza urządzeniami z systemem Windows 10 za pośrednictwem integracji usługi Azure AD. Systemy MDM innych firm dla typów systemu operacyjnego urządzenia innych niż Windows 10 nie są obsługiwane.
 
![Warunki oparte na urządzeniu](./media/require-managed-devices/46.png)

W przypadku urządzenia oznaczonego jako zgodnego można założyć, że: 

- Urządzenia mobilne używane przez pracowników do uzyskiwania dostępu do danych firmy są zarządzane
- Aplikacje mobilne, z których korzystają pracownicy, są zarządzane
- Informacje o firmie są chronione, pomagając kontrolować sposób, w jaki pracownicy uzyskują do nich dostęp i udostępniają je
- Urządzenie i jego aplikacje są zgodne z wymaganiami firmy dotyczącymi bezpieczeństwa

### <a name="known-behavior"></a>Znane zachowanie

W systemie Windows 7, iOS, Android, macOS i niektórych przeglądarkach internetowych innych firm usługi Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest aprowidyfikowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD. Gdy użytkownik po raz pierwszy loguje się za pośrednictwem przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik końcowy musi wybrać ten certyfikat, zanim będzie mógł nadal korzystać z przeglądarki.

## <a name="next-steps"></a>Następne kroki

Przed skonfigurowaniem zasad dostępu warunkowego opartego na urządzeniach w twoim środowisku należy przyjrzeć się [najlepszym praktykom dotyczącym dostępu warunkowego w usłudze Azure Active Directory](best-practices.md).
