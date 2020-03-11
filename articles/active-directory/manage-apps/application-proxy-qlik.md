---
title: Serwer Proxy aplikacji usługi Azure AD i usługa Qlik Sense | Dokumentacja firmy Microsoft
description: Włącz serwer Proxy aplikacji w witrynie Azure portal i zainstaluj łączniki dla zwrotnego serwera proxy.
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
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036996"
---
# <a name="application-proxy-and-qlik-sense"></a>Serwer Proxy aplikacji i usługa Qlik Sense 
Usługa Azure Active Directory serwera Proxy aplikacji i usługa Qlik Sense wspólnie opracowały razem upewnij się, że możesz łatwo udostępnić dostępu zdalnego we wdrożeniu usługa Qlik Sense za pomocą serwera Proxy aplikacji.  

## <a name="prerequisites"></a>Wymagania wstępne 
W pozostałej części w tym scenariuszu przyjęto założenie, że wykonać następujące czynności:
 
- Skonfigurowany [Qlik](https://community.qlik.com/docs/DOC-19822). 
- [Zainstalowano łącznik serwera proxy aplikacji](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikuj swoje aplikacje na platformie Azure 
Aby opublikować QlikSense, należy opublikować dwóch aplikacji na platformie Azure.  

### <a name="application-1"></a>Aplikacja #1: 
Wykonaj następujące kroki, aby opublikować aplikację. Aby zapoznać się z bardziej szczegółowym omówieniem kroków 1-8, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). 


1. Zaloguj się do witryny Azure portal jako administrator globalny. 
2. Wybierz pozycję **Azure Active Directory** > **aplikacje dla przedsiębiorstw**. 
3. Wybierz pozycję **Dodaj** w górnej części bloku. 
4. Wybierz **aplikację lokalną**. 
5. Wypełnij wymagane pola informacji o nowej aplikacji. Użyj poniższych wskazówek, aby ustawienia: 
   - **Wewnętrzny adres URL**: Ta aplikacja powinna mieć wewnętrzny adres URL, który jest samym adresem URL QlikSense. Na przykład **https&#58;//demo.qlikemm.com:4244** 
   - **Metoda wstępnego uwierzytelniania**: Azure Active Directory (zalecane, ale nie wymagane) 
1. Wybierz pozycję **Dodaj** w dolnej części bloku. Aplikacja zostanie dodany, a zostanie otwarte menu skrócone. 
2. W menu szybki Start wybierz pozycję **Przypisz użytkownika do testowania**i Dodaj co najmniej jednego użytkownika do aplikacji. Upewnij się, że to konto testu ma dostęp do aplikacji w środowisku lokalnym. 
3. Wybierz pozycję **Przypisz** , aby zapisać testowe przypisanie użytkownika. 
4. (Opcjonalnie) W bloku zarządzania aplikacji wybierz opcję logowania jednokrotnego. Wybierz **ograniczone delegowanie protokołu Kerberos** z menu rozwijanego i Wypełnij wymagane pola w oparciu o konfigurację Qlik. Wybierz pozycję **Zapisz**. 

### <a name="application-2"></a>Aplikacja #2: 
Te same czynności jak w przypadku aplikacji nr 1, z następującymi wyjątkami: 

**Krok #5**: wewnętrzny adres URL powinien teraz być adresem URL QlikSense z portem uwierzytelniania używanym przez aplikację. Wartość domyślna to **4244** dla protokołów HTTPS i **4248** dla protokołu HTTP dla wersji QlikSense wcześniejszych niż kwiecień 2018. Domyślne wersje QlikSense po kwietnia 2018 to **443** dla protokołów HTTPS i **80** dla protokołu HTTP.  Np. **https&#58;//demo.Qlik.com:4244**</br></br>
**#10 krok:** Nie Konfiguruj logowania jednokrotnego i pozostaw **Logowanie** jednokrotne wyłączone
 
 
## <a name="testing"></a>Testowanie 
Twoja aplikacja jest teraz gotowe do testu. Dostęp do zewnętrznego adresu URL używany do publikowania QlikSense w aplikacji nr 1, a następnie zaloguj się jako użytkownik przypisany do obydwu aplikacji.  

## <a name="additional-references"></a>Dodatkowa dokumentacja
Aby uzyskać więcej informacji na temat publikowania Qlik z serwerem proxy aplikacji, zapoznaj się z następującymi artykułami społeczności Qlik: 
- [Usługa Azure AD z zintegrowanym uwierzytelnianiem systemu Windows przy użyciu ograniczonego delegowania protokołu Kerberos z Qlik sensem](https://community.qlik.com/docs/DOC-20183)
- [Integracja Qlik z platformą Azure serwer proxy aplikacji usługi Azure AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Następne kroki

- [Publikowanie aplikacji przy użyciu serwera proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Praca z łącznikami serwera proxy aplikacji](application-proxy-connector-groups.md)

