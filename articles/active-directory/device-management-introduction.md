---
title: Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie urządzeniami może pomóc uzyskać kontrolę nad urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1a2b32e7e0e86608f879941485395ebe379a5c1e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048865"
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory

W świecie urządzeń przenośnych i chmurze — Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. Powszechne stosowanie urządzeń — w tym urządzenia (PRZYNIEŚ własne) specjalistów IT muszą stawiać dwa przeciwstawnym cele:

- Zwiększenie możliwości dostępnych dla użytkowników końcowych do wydajnej wszędzie tam, gdzie i kiedy
- Ochrona zasobów firmowych w dowolnym momencie

Za pomocą urządzeń użytkownicy otrzymują dostęp do zasobów firmowych. Aby chronić Twoje zasoby firmowe, administrator IT, chcesz mieć kontrolę nad tymi urządzeniami. Dzięki temu można się upewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. 

Zarządzanie urządzeniami jest również podstawą dla [dostępu warunkowego opartego na urządzeniu](active-directory-conditional-access-policy-connected-applications.md). Przy użyciu dostępu warunkowego opartego na urządzeniach można zagwarantować, że dostęp do zasobów w środowisku jest możliwe tylko przy użyciu zarządzanych urządzeń.   

W tym artykule opisano, jak działa zarządzanie urządzeniami w usłudze Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Pobieranie urządzeń pod kontrolą programu Azure AD

Aby uzyskać urządzenie pod kontrolą programu Azure AD, masz dwie opcje:

- Rejestrowanie 
- Sprzęganie

**Rejestrowanie** urządzenia do usługi Azure AD umożliwia zarządzanie tożsamościami urządzeń. Po zarejestrowaniu urządzenia usługa rejestracja urządzeń w usłudze Azure AD Inicjuje obsługę urządzenia przy użyciu tożsamości, który jest używany do uwierzytelniania urządzenia, gdy użytkownik logowania do usługi Azure AD. Tożsamość można użyć, aby włączyć lub wyłączyć urządzenia.

W przypadku połączenia urządzenia przenośnego z rozwiązaniem jak usługa Microsoft Intune, atrybuty urządzenia w usłudze Azure AD są aktualizowane z dodatkowymi informacjami o urządzeniu. Umożliwia to tworzenie reguł dostępu warunkowego, które wymuszają dostęp z urządzeń spełniających określone standardy zabezpieczeń i zgodności. Aby uzyskać więcej informacji na temat rejestrowania urządzeń w programie Microsoft Intune Zobacz rejestrowanie urządzeń do zarządzania w usłudze Intune.

**Łączenie** urządzenia jest rozszerzeniem z rejestracją urządzenia. Oznacza to, że zapewnia ona wszystkie zalety rejestrowania urządzenia i oprócz tego zmienia także lokalne stan urządzenia. Zmiany lokalne stanu użytkownikom zalogować się do urządzenia przy użyciu pracy organizacji lub konta służbowego, a nie konta osobistego.

## <a name="azure-ad-registered-devices"></a>Urządzeń zarejestrowanych w usłudze Azure AD   

Celem usługi Azure AD, zarejestrowanych urządzeń jest umożliwiają obsługę **Przenieś swój własny urządzenia (BYOD)** scenariusza. W tym scenariuszu użytkownik mógł korzystać z zasobów usługi Azure Active Directory kontrolowane organizacji za pomocą urządzeń osobistych.  

![Urządzeń zarejestrowanych w usłudze Azure AD](./media/device-management-introduction/03.png)

Dostępu opiera się na konto służbowe lub szkolne, który został wprowadzony na urządzeniu.  
Na przykład systemu Windows 10 pozwala użytkownikom dodawanie konta firmowego lub szkolnego do komputerów osobistych, tabletu lub telefonu.  
Po użytkownik został dodany pracy konta służbowego, urządzenie jest zarejestrowane w usłudze Azure AD i opcjonalnie zarejestrowane w systemie zarządzania (urządzeniami przenośnymi MDM) urządzenia przenośnego, które Twoja organizacja ma skonfigurowane. Użytkowników w organizacji może dodać konta służbowego do urządzenia osobistego wygodny sposób:

- Podczas uzyskiwania dostępu do aplikacji pracy, po raz pierwszy
- Ręcznie za pośrednictwem **ustawienia** menu w przypadku systemu Windows 10 

Możesz skonfigurować urządzenia w usłudze Azure AD zarejestrowany dla systemu Windows 10, iOS, Android i macOS.

## <a name="azure-ad-joined-devices"></a>Urządzenia przyłączone do usługi Azure AD

Celem urządzeń przyłączonych do usługi Azure AD jest uproszczenie:

- Wdrożenia Windows urządzenia należące do pracy 
- Dostęp do aplikacji i zasobów organizacji na dowolnym urządzeniu — Windows
- Oparte na chmurze zarządzanie urządzeniami należącymi do pracy

![Urządzeń zarejestrowanych w usłudze Azure AD](./media/device-management-introduction/02.png)

Przyłączanie do usługi Azure AD można wdrożyć przy użyciu dowolnej z następujących metod: 
 - [Rozwiązania Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Wdrażanie zbiorcze](https://docs.microsoft.com/intune/windows-bulk-enroll)
 - [Samoobsługowe](device-management-azuread-joined-devices-frx.md) 

**Funkcja Azure AD Join** jest przeznaczona dla organizacji, które mają być przede (oznacza to przede wszystkim za pomocą usług w chmurze, której celem jest ograniczyć korzystanie z infrastruktury lokalnej) lub tylko w chmurze (nie infrastruktury lokalnej). Nie ma żadnych ograniczeń rozmiaru i rodzaju organizacje, które można wdrożyć w usłudze Azure AD Join. Azure AD Join działa dobrze nawet w środowisku hybrydowym, umożliwiając dostęp do aplikacji w chmurze i lokalnych i zasobów.

Implementowanie urządzeń przyłączonych do usługi Azure AD zapewnia następujące korzyści:

- **Logowanie jednokrotne (SSO)** do subskrypcji platformy Azure zarządzanych usług i aplikacji SaaS. Użytkownicy nie zobaczą monity o dodatkowe uwierzytelnianie podczas uzyskiwania dostępu do zasobów służbowych. Funkcji logowania jednokrotnego jest nawet wtedy, gdy nie są połączeni z siecią domeny dostępne.

- **Roaming zgodne przedsiębiorstw** ustawień użytkownika w dołączonym do urządzenia. Użytkownicy nie muszą połączyć konto Microsoft (na przykład usługi Hotmail), aby wyświetlić ustawienia między urządzeniami.

- **Dostęp do Windows Store dla firm** przy użyciu konta usługi Azure AD. Użytkownicy mogą wybierać spis aplikacji wstępnie wybrana przez organizację.

- **Windows Hello** Obsługa bezpieczny i wygodny dostęp do zasobów służbowych.

- **Ograniczenie dostępu** aby aplikacji tylko w przypadku urządzeń, które spełniają zasady zgodności.

- **Bezproblemowy dostęp do zasobów lokalnych** Jeśli urządzenie ma bezpośredni kontakt z kontrolerem domeny w środowisku lokalnym. 


Podczas dołączania do usługi Azure AD jest przeznaczone głównie dla organizacji, które nie mają takiej infrastruktury, Windows Server Active Directory, możesz bez obaw można używać go w scenariuszach gdzie:

- Aby przejść do infrastruktury opartej na chmurze, korzystając z usługi Azure AD i usługą MDM, takich jak usługi Intune.

- Przyłączanie do domeny w środowisku lokalnym nie można użyć na przykład, jeśli zajdzie potrzeba przywrócenia urządzeń przenośnych, takich jak tablety i telefony pod kontrolą.

- Użytkownicy są głównie potrzebują dostępu do usługi Office 365 lub innych aplikacji SaaS zintegrowanych z usługą Azure AD.

- Chcesz zarządzać grupy użytkowników w usłudze Azure AD, a nie w usłudze Active Directory. Może to dotyczyć, na przykład pracownikom sezonowym, Zleceniobiorcami ani studentów.

- Chcesz zapewnić możliwości przyłączany do pracowników w biurach oddziałów zdalnego przy użyciu ograniczone na infrastrukturę lokalną.

Możesz skonfigurować urządzenia dołączone do usługi Azure AD dla urządzeń z systemem Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia przyłączone do hybrydowej usługi Azure AD

Przez ponad dekadę wiele organizacji ma umożliwiają przyłączanie do domeny, aby ich w lokalnej usłudze Active Directory:

- Działy IT do zarządzania urządzeniami należącymi do pracy z centralnej lokalizacji.

- Użytkownicy mogą logować się do ich urządzeń z usługą Active Directory się kont służbowych. 

Zazwyczaj organizacjom zużycie lokalnych zależą od tego, metody, aby aprowizować urządzenia do tworzenia obrazów i często używają **programu System Center Configuration Manager (SCCM)** lub **zasad (zasad grupy) grupy** do zarządzania je.

Jeśli w lokalnym środowisku zużycie AD, a także skorzystać z możliwości oferowane przez usługę Azure Active Directory, można zaimplementować urządzeń przyłączonych do usługi Azure AD hybrydowych. Są to urządzenia, które są zarówno do lokalnej usługi Active Directory i usługi Azure Active Directory.

![Urządzeń zarejestrowanych w usłudze Azure AD](./media/device-management-introduction/01.png)


Urządzenia przyłączone do hybrydowej usługi Azure AD należy używać, jeśli:

- Masz wdrożonych na tych urządzeniach, które korzystają z usługi Active Directory, uwierzytelnianie komputera aplikacji Win32.

- Wymagane jest zasad grupy do zarządzania urządzeniami.

- Chcesz kontynuować korzystanie z rozwiązań przetwarzania obrazów umożliwiają konfigurowanie urządzeń dla pracowników.

Można skonfigurować hybrydowych usługi Azure AD urządzenia przyłączone do systemu Windows 10 i urządzeń niskiego poziomu, takich jak Windows 8 i Windows 7.

## <a name="summary"></a>Podsumowanie

Za pomocą zarządzania urządzeniami w usłudze Azure AD możesz wykonywać następujące czynności: 

- Upraszcza proces urządzeń znajdujących się pod kontrolą programu Azure AD

- Zapewnić użytkownikom łatwy w użyciu dostęp do zasobów w chmurze w organizacji

Zgodnie z zasadą kciuka należy użyć:

- Urządzeń zarejestrowanych w usłudze Azure AD:

    - Dla urządzeń osobistych: 

    - Aby ręcznie zarejestrować urządzenia z usługą Azure AD

- Urządzenia przyłączone do usługi Azure AD: 

    - W przypadku urządzeń, które są własnością Twojej organizacji

    - W przypadku urządzeń, które są **nie** przyłączone do lokalnej usługi AD

    - Aby ręcznie zarejestrować urządzenia z usługą Azure AD

    - Aby zmienić stan lokalnego urządzenia

- Urządzenia dla urządzeń, które są przyłączone do lokalnej przyłączone do hybrydowej usługi Azure AD AD     

    - W przypadku urządzeń, które są własnością Twojej organizacji

    - W przypadku urządzeń, które są przyłączone do lokalnej usługi AD

    - Aby automatycznie zarejestrować urządzenia z usługą Azure AD

    - Aby zmienić stan lokalnego urządzenia



## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure portal, zobacz [zarządzania urządzeniami przy użyciu witryny Azure portal](device-management-azure-portal.md)

- Aby dowiedzieć się więcej na temat dostępu warunkowego opartego na urządzeniach, zobacz [konfigurowania zasad dostępu warunkowego opartego na urządzeniu usługi Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

- Do instalacji:
    - Usługa Azure Active Directory zarejestrowanych urządzeń z systemem Windows 10, zobacz [sposób konfigurowania usługi Azure Active Directory zarejestrowanych urządzeń z systemem Windows 10](user-help/device-management-azuread-registered-devices-windows10-setup.md)
    - Urządzenia przyłączone do usługi Azure Active Directory, zobacz [urządzeń przyłączonych do konfigurowania usługi Azure Active Directory](user-help/device-management-azuread-joined-devices-setup.md)
    - Hybrydowe urządzeń przyłączonych do usługi Azure AD, zobacz [w jaki sposób skonfigurować hybrydowych urządzeń w przyłączonych do usługi Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md).


