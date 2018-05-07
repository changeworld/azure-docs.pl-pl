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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 6b1edb9c4574afa77df43e4f017848acd3ae6d28
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory

W świecie pierwszy mobile, najpierw chmury Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. Rosnąca liczba urządzeń — łącznie z urządzenia (PRZYNIEŚ własne) specjalistów IT muszą stawiać czoła dwóch celów przeciwna:

- Zwiększenie możliwości dostępnych dla użytkowników końcowych do produktywność wszędzie tam, gdzie i zawsze, gdy
- Ochrona zasobów firmy w dowolnym momencie

Za pomocą urządzeń użytkownicy są uzyskiwania dostępu do zasobów firmowych. Aby chronić z zasobami firmy jako IT administrator, chcesz mają kontrolę nad tymi urządzeniami. Dzięki temu można upewnij się, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają standardy zabezpieczeń i zgodności. 

Zarządzanie urządzeniami jest również podstawą [dostępu warunkowego opartego na urządzeniu](active-directory-conditional-access-policy-connected-applications.md). Przy użyciu dostępu warunkowego opartego na urządzeniach można zapewnić, że dostęp do zasobów w środowisku jest możliwe tylko z zaufanych urządzeń.   

W tym temacie wyjaśniono, jak działa zarządzanie urządzeniami w usłudze Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Pobieranie urządzeń pod kontrolą programu Azure AD

Aby uzyskać urządzenia pod kontrolą programu Azure AD, masz dwie opcje:

- Rejestrowanie 
- Łączenie

**Rejestrowanie** urządzenia do usługi Azure AD pozwala na zarządzanie tożsamości urządzenia. Po zarejestrowaniu urządzenia rejestracji urządzeń usługi Azure AD zapewnia urządzenia przy użyciu tożsamości, który jest używany do uwierzytelniania urządzenia, gdy użytkownik loguje się w usłudze Azure AD. Korzystania z tożsamości, aby włączyć lub wyłączyć urządzenia.

W połączeniu z rozwiązaniem management(MDM) urządzeń przenośnych, takich jak Microsoft Intune, atrybuty urządzenia w usłudze Azure AD są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Umożliwia to tworzenie reguł dostępu warunkowego, które wymuszają dostęp z urządzeń spełniających określone standardy zabezpieczeń i zgodności. Aby uzyskać więcej informacji dotyczących rejestrowania urządzeń w Microsoft Intune Zobacz rejestrowanie urządzeń do zarządzania w usłudze Intune.

**Sprzęganie** urządzenie to rozszerzenie rejestrowania urządzenia. Oznacza to, że zapewnia ona wszystkich funkcji rejestrowania urządzenia i oprócz tego również zmieni stan lokalnego urządzenia. Zmiana stanu lokalnego użytkownikom zalogować się do urządzenia przy użyciu organizacji konta firmowego lub szkolnego, a nie konta osobistego.

## <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane usługi Azure AD   

Celem usługi Azure AD, zarejestrowane urządzenia jest umożliwiają obsługę **urządzenia (PRZYNIEŚ własne)** scenariusza. W tym scenariuszu użytkownik mógł korzystać z zasobów usługi Azure Active Directory pod kontrolą firmy za pomocą urządzeń osobistych.  

![Urządzenia zarejestrowane usługi Azure AD](./media/device-management-introduction/03.png)

Dostęp jest oparty na konta firmowego lub szkolnego, który został wprowadzony na urządzeniu.  
Na przykład systemu Windows 10 pozwala użytkownikom na dodawanie konta firmowego lub szkolnego do komputerów osobistych, tabletu lub telefonu.  
Gdy użytkownik został dodany firmowego konta służbowego, urządzenie jest zarejestrowane w usłudze Azure AD i opcjonalnie zarejestrowane w systemie zarządzania urządzeniami Przenośnymi urządzeniami przenośnymi, skonfigurowanego w Twojej organizacji. Użytkownicy w organizacji można dodać konto służbowe do urządzenia osobistego wygodny sposób:

- Podczas uzyskiwania dostępu do aplikacji pracy po raz pierwszy
- Ręcznie za pośrednictwem **ustawienia** menu w przypadku systemu Windows 10 

Możesz skonfigurować urządzenia usługi Azure AD w zarejestrowany dla systemu Windows 10, iOS, Android i macOS.

## <a name="azure-ad-joined-devices"></a>Urządzeniach przyłączonych do usługi Azure AD

Celem urządzeń usługi Azure AD przyłączony jest uproszczenie:

- Wdrażanie systemu Windows urządzenia należące do pracy 
- Dostęp do zasobów i aplikacji organizacji z dowolnego urządzenia z systemem Windows

![Urządzenia zarejestrowane usługi Azure AD](./media/device-management-introduction/02.png)


Te cele są realizowane przez zapewnienie użytkownikom przy użyciu środowiska samodzielnego uzyskania urządzenia należące do pracy pod kontrolą programu Azure AD.  
**Azure AD Join** jest przeznaczona dla organizacji, które chcesz najpierw chmury lub tylko w chmurze. Brak ograniczeń rozmiaru lub typ organizacji, które można wdrożyć w usłudze Azure AD Join nie istnieje. Azure AD Join działa prawidłowo nawet w przypadku środowiska hybrydowego, a można włączyć dostęp do lokalnych aplikacji i zasobów.

Wdrażanie usługi Azure AD przyłączone do urządzeń zapewnia następujące korzyści:

- **Single-Sign-On rejestracji jednokrotnej (SSO)** platformy Azure zarządzanych usług i aplikacji SaaS. Użytkownicy nie zobaczą monity o dodatkowe uwierzytelnianie podczas uzyskiwania dostępu do zasobów roboczych. Funkcja logowania jednokrotnego jest nawet wtedy, gdy nie są połączeni z siecią domeny dostępne.

- **Roaming zgodne Enterprise** ustawień użytkownika na urządzeniach połączonych. Użytkownicy nie muszą łączyć konta Microsoft (na przykład usługi Hotmail), aby wyświetlić ustawienia między urządzeniami.

- **Dostęp do Sklepu Windows dla firm** przy użyciu konta usługi Azure AD. Użytkownicy, można wybrać ze spisu aplikacji wstępnie wybrany przez organizację.

- **Usługi Windows Hello** obsługę bezpiecznej i wygodny dostęp do zasobów.

- **Ograniczenie dostępu** aby aplikacji tylko w przypadku urządzeń, które spełniają zasady zgodności.

- **Bezproblemowy dostęp do zasobów na lokalnych** gdy urządzenie ma procesów z wiersza do lokalnego kontrolera domeny.


Podczas usługi Azure AD join jest przeznaczone głównie dla organizacji, które nie mają infrastruktury lokalnej usługi Active Directory systemu Windows Server, można wykonywać następujące czynności na pewno również korzystać w scenariuszach, w których:

- Przyłączanie do domeny lokalnej nie można użyć na przykład, jeśli potrzebujesz urządzeń przenośnych, takich jak tablety i telefony pod kontrolą.

- Przede wszystkim użytkownicy musieli uzyskiwać dostęp do usługi Office 365 lub innych aplikacji SaaS zintegrowanych z usługą Azure AD.

- Chcesz zarządzać grupy użytkowników w usłudze Azure AD, a nie w usłudze Active Directory. To może dotyczyć, na przykład okresach pracownicy, wykonawcy lub studentów.

- Chcesz podać łącząca możliwości do pracowników w biurach oddziałów zdalnego z infrastruktury lokalnej ograniczone.

Możesz skonfigurować urządzenia usługi Azure AD połączone dla urządzeń z systemem Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Urządzeniach przyłączonych do hybrydowej usługi Azure AD

Przez ponad dekadę wiele organizacji ma umożliwia przyłączenie do domeny w usłudze Active Directory ich lokalnych:

- Działy IT, aby zarządzać urządzeniami należącymi do pracy z centralnej lokalizacji.

- Użytkownikom na logowanie na urządzeniach z usługą Active Directory ich służbowe kont. 

Zazwyczaj organizacji mających wpływ na lokalnym zależne imaging metody inicjowania obsługi urządzeń, a często używane **programu System Center Configuration Manager (SCCM)** lub **(GP) zasad grupy** do zarządzania je.

Jeśli środowisko ma lokalnego wpływ AD a również skorzystać z możliwości oferowane przez usługi Azure Active Directory, można zaimplementować hybrydowe usługi Azure AD przyłączone do urządzeń. Są to urządzenia, które są zarówno do lokalnej usługi Active Directory i Azure Active Directory.

![Urządzenia zarejestrowane usługi Azure AD](./media/device-management-introduction/01.png)


Należy używać urządzeń hybrydowego przyłączonych do usługi Azure AD, jeśli:

- Masz aplikacji Win32 wdrożonych na tych urządzeniach, które używają protokołu NTLM / Kerberos.

- Wymagaj zasad grupy lub SCCM / DCM do zarządzania urządzeniami.

- Chcesz nadal używać do konfigurowania urządzeń pracowników rozwiązań do obsługi obrazów.

Można skonfigurować hybrydowe usługi Azure AD urządzeniach przyłączonych do systemu Windows 10 i urządzeniach niskiego poziomu, takich jak Windows 8 i Windows 7.

## <a name="summary"></a>Podsumowanie

Zarządzanie urządzeniami w usłudze Azure AD możesz: 

- Uprościć proces zapewniania urządzeń pod kontrolą programu Azure AD

- Zapewnić użytkownikom łatwe w użyciu dostęp do zasobów w chmurze organizacji

Zgodnie z zasadą thumb należy użyć:

- Usługi Azure AD zarejestrowanych urządzeń:

    - W przypadku urządzeń osobistych 

    - Aby ręcznie zarejestrować urządzenia z usługi Azure AD

- Urządzeniach przyłączonych do usługi Azure AD: 

    - Dla urządzeń, które są własnością Twojej organizacji

    - W przypadku urządzeń, które są **nie** przyłączone do lokalnej usługi AD

    - Aby ręcznie zarejestrować urządzenia z usługi Azure AD

    - Aby zmienić stan lokalnego urządzenia

- Hybrydowe usługi Azure AD połączone urządzenia dla urządzeń, które są częścią lokalnej usługi AD     

    - Dla urządzeń, które są własnością Twojej organizacji

    - Dla urządzeń, które są częścią lokalnej usługi AD

    - Do automatycznego rejestrowania urządzeń w usłudze Azure AD

    - Aby zmienić stan lokalnego urządzenia



## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd sposobu zarządzania urządzeniami w portalu Azure, zobacz [zarządzanie urządzeniami przy użyciu portalu Azure](device-management-azure-portal.md)

- Aby dowiedzieć się więcej na temat dostępu warunkowego opartego na urządzeniu, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzenia usługi Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

- Do instalacji:
    - Usługa Azure Active Directory zarejestrowanych urządzeń z systemem Windows 10, zobacz [sposób konfigurowania usługi Azure Active Directory zarejestrowanych urządzeń z systemem Windows 10](device-management-azuread-registered-devices-windows10-setup.md)
    - Urządzeniach przyłączonych do usługi Azure Active Directory, zobacz [urządzeniach przyłączonych do konfigurowania usługi Azure Active Directory](device-management-azuread-joined-devices-setup.md)
    - Hybrydowe usługi Azure AD przyłączone do urządzenia, zobacz [jak do skonfigurowania hybrydowego przyłączonych do usługi Azure Active Directory urządzeń](device-management-hybrid-azuread-joined-devices-setup.md).


