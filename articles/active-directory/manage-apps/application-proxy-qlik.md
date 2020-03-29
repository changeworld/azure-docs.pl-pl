---
title: Serwer proxy aplikacji usługi Azure AD i Qlik Sense| Dokumenty firmy Microsoft
description: Włącz serwer proxy aplikacji w witrynie Azure portal i zainstaluj łączniki dla odwrotnego serwera proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036996"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy aplikacji i Qlik Sense 
Usługi Azure Active Directory Application Proxy i Qlik Sense nawiązały współpracę, aby zapewnić łatwy dostęp do serwera proxy aplikacji w celu zapewnienia zdalnego dostępu do wdrożenia Qlik Sense.  

## <a name="prerequisites"></a>Wymagania wstępne 
Pozostała część tego scenariusza zakłada, że wykonasz następujące czynności:
 
- Skonfigurowany [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Instalowanie łącznika serwera proxy aplikacji](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikowanie aplikacji na platformie Azure 
Aby opublikować QlikSense, należy opublikować dwie aplikacje na platformie Azure.  

### <a name="application-1"></a>#1 aplikacji: 
Wykonaj następujące kroki, aby opublikować aplikację. Aby uzyskać bardziej szczegółowe wskazówki dotyczące kroków 1-8, zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). 


1. Zaloguj się w witrynie Azure Portal jako administrator globalny. 
2. Wybierz **aplikacje Azure Active Directory** > **Enterprise**. 
3. Wybierz **pozycję Dodaj** w górnej części bloku. 
4. Wybierz **opcję Aplikacja lokalna**. 
5. Wypełnij wymagane pola informacjami o nowej aplikacji. Użyj następujących wskazówek dotyczących ustawień: 
   - **Wewnętrzny adres URL:** Ta aplikacja powinna mieć wewnętrzny adres URL, który jest samym adresem URL QlikSense. Na przykład **https&#58;//demo.qlikemm.com:4244** 
   - **Metoda uwierzytelniania wstępnego:** Usługa Azure Active Directory (zalecana, ale nie jest wymagana) 
1. Wybierz **pozycję Dodaj** u dołu ostrza. Aplikacja zostanie dodana i otworzy się menu szybkiego startu. 
2. W menu szybki start wybierz pozycję **Przypisz użytkownika do testowania**i dodaj do aplikacji co najmniej jednego użytkownika. Upewnij się, że to konto testowe ma dostęp do aplikacji lokalnej. 
3. Wybierz **pozycję Przypisz,** aby zapisać przypisanie użytkownika testowego. 
4. (Opcjonalnie) W bloku zarządzania aplikacjami wybierz pozycję Logowanie jednokrotne. Z menu rozwijanego wybierz **polecenie Delegowanie ograniczone kerberos** i wypełnij wymagane pola na podstawie konfiguracji Qlik. Wybierz **pozycję Zapisz**. 

### <a name="application-2"></a>#2 aplikacji: 
Wykonaj te same kroki, co w przypadku #1 aplikacji, z następującymi wyjątkami: 

**Krok #5:** Wewnętrzny adres URL powinien być teraz adresem URL QlikSense z portem uwierzytelniania używanym przez aplikację. Wartość domyślna to **4244** dla protokołu HTTPS i **4248** dla wersji HTTP dla wersji QlikSense przed kwietniem 2018 r. Domyślnie dla wersji QlikSense po kwietniu 2018 r. jest **443** dla HTTPS i **80** dla HTTP.  Przykład: **https&#58;//demo.qlik.com:4244**</br></br>
**Krok #10:** Nie konfigurowanie logowania jednokrotnego i pozostawienie **logowania jednokrotnego wyłączonego**
 
 
## <a name="testing"></a>Testowanie 
Aplikacja jest teraz gotowa do testowania. Dostęp do zewnętrznego adresu URL użytego do publikowania qliksense w aplikacji #1 i logowania jako użytkownik przypisany do obu aplikacji.  

## <a name="additional-references"></a>Dodatkowa dokumentacja
Aby uzyskać więcej informacji na temat publikowania Qlik Sense z pełnomocnikiem aplikacji, zapoznaj się z następującymi artykułami społeczności Qlik: 
- [Usługa Azure AD ze zintegrowanym uwierzytelnianiem systemu Windows przy użyciu delegowania ograniczonego protokołu Kerberos z programem Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Integracja qlik sense z serwerem proxy aplikacji usługi Azure AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Następne kroki

- [Publikowanie aplikacji za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Praca ze złączami serwera proxy aplikacji](application-proxy-connector-groups.md)

