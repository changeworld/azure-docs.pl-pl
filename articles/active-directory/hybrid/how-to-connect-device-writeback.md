---
title: 'Program Azure AD Connect: Włączanie zapisywania zwrotnego urządzeń | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera szczegółowe informacje dotyczące włączania zapisywania zwrotnego urządzeń za pomocą usługi Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ed6fd0a4a1de3fb02b3d8583c0e5c0cecac211
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351842"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Program Azure AD Connect: Włączanie zapisywania zwrotnego urządzeń
> [!NOTE]
> Subskrypcja usługi Azure AD Premium jest wymagana na potrzeby zapisywania zwrotnego urządzeń.
> 
> 

Poniższa dokumentacja zawiera informacje o sposobie włączania funkcji zapisywania zwrotnego urządzeń w usłudze Azure AD Connect. Zapisywanie zwrotne urządzeń jest używany w następujących scenariuszach:

* Włączanie dostępu warunkowego opartego na urządzeniach do usług AD FS (2012 R2 lub nowszy) chronione aplikacje (jednostek uzależnionych).

Dzięki temu zwiększa bezpieczeństwo i poziom gwarancji, że udzielono dostępu do aplikacji tylko do zaufanych urządzeń. Aby uzyskać więcej informacji na temat dostępu warunkowego, zobacz [zarządzanie ryzykiem przy użyciu dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) i [Konfigurowanie lokalnego dostępu warunkowego przy użyciu usługi Azure Active Directory rejestracji urządzenia](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Urządzenia muszą znajdować się w tym samym lesie co użytkowników. Ponieważ urządzenia musi zapisywane do pojedynczego lasu, ta funkcja nie obsługuje obecnie wdrożenia z wieloma lasami użytkownika.</li>
> <li>Obiekt konfiguracji rejestracji tylko jedno urządzenie można dodać do lasu usługi Active Directory w środowisku lokalnym. Ta funkcja nie jest zgodne z topologią, w którym w lokalnej usłudze Active Directory jest synchronizowane z wieloma katalogami usługi Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Część 1: Instalowanie programu Azure AD Connect
Zainstaluj program Azure AD Connect przy użyciu niestandardowych lub ustawienia ekspresowe. Firma Microsoft zaleca rozpocząć od wszystkich użytkowników i grup pomyślnie zsynchronizowano zanim włączysz zapisywanie zwrotne urządzeń.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Część 2: Włączanie zapisywania zwrotnego urządzeń w usłudze Azure AD Connect
1. Uruchom ponownie Kreatora instalacji. Wybierz **Konfiguruj opcje urządzenia** z zadania dodatkowe strony, a następnie kliknij przycisk **dalej**. 

    ![Konfiguruj opcje urządzenia](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Nowe Konfiguruj opcje urządzenia jest dostępna tylko w wersji 1.1.819.0 lub nowszej.

2. Na stronie opcji urządzenia, wybierz **skonfiguruj zapisywanie zwrotne urządzeń**. Opcję **Wyłącz zapisywanie zwrotne urządzeń** nie będą dostępne do czasu włączenia zapisywania zwrotnego urządzeń. Kliknij pozycję **dalej** aby przejść do następnej strony kreatora.
    ![Wybrana operacja urządzenia](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na stronie funkcji zapisywania zwrotnego podana domena zostanie wyświetlona jako las zapisywania zwrotnego urządzeń domyślne.
   ![Niestandardowe lasem docelowym zapisywania zwrotnego urządzeń instalacji](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Kontener urządzeń** strona zapewnia opcję przygotowywania usługi active directory przy użyciu jednej z dostępnych opcji:

    a. **Podaj poświadczenia administratora przedsiębiorstwa**: Jeśli nie są podane poświadczenia administratora przedsiębiorstwa dla lasu, w którym urządzenia muszą być zapisywane z powrotem, program Azure AD Connect spowoduje automatyczne przygotowanie lasu podczas konfigurowania zapisywania zwrotnego urządzeń.

    b. **Pobierz skrypt programu PowerShell**: Program Azure AD Connect automatycznie generuje skrypt programu PowerShell, którego można przygotować usługi active directory do zapisywania zwrotnego urządzeń. W przypadku, gdy nie można podać poświadczenia administratora przedsiębiorstwa w programie Azure AD Connect, zaleca się pobrać skrypt programu PowerShell. Podaj pobranego skryptu programu PowerShell **CreateDeviceContainer.psq** do administratora przedsiębiorstwa w lesie, w którym urządzenia będzie można zapisać zwrotnie w.
    ![Przygotowanie lasu usługi active directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Następujące operacje są wykonywane w przypadku przygotowywania lasu usługi active directory:
    * Jeśli ich jeszcze nie istnieje, tworzy i konfiguruje nowe kontenery i obiekty CN = Configuration rejestracji urządzeń, CN = Services, CN = Configuration, [nazwa wyróżniająca lasu].
    * Jeśli ich jeszcze nie istnieje, tworzy i konfiguruje nowe kontenery i obiekty CN = RegisteredDevices, [nazwa wyróżniająca domeny]. Obiekty urządzeń zostanie utworzona w tym kontenerze.
    * Ustawia uprawnienia niezbędne konta łącznika usługi Azure AD w celu zarządzania urządzeniami w usłudze Active Directory.
    * Musi zostać uruchomiony jedynie na jednym lesie, nawet jeśli program Azure AD Connect jest instalowany w wielu lasach.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Sprawdź, czy urządzenia są synchronizowane z usługi Active Directory
Zapisywanie zwrotne urządzeń powinien teraz działać prawidłowo. Należy pamiętać, że może potrwać do 3 godzin obiekty urządzeń, które ma być zapisany do tyłu dla usługi AD.  Aby sprawdzić, czy urządzenia są synchronizowanego prawidłowo, wykonaj następujące czynności po wykonaniu reguły synchronizacji:

1. Uruchom Centrum administracyjne usługi Active Directory.
2. Rozwiń RegisteredDevices w domenie, które są Sfederowane.

   ![Urządzeń zarejestrowanych w Centrum administracyjnym usługi Active Directory](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Zostaną wyświetlone bieżące zarejestrowanych urządzeń.

   ![Centrum administracyjne usługi Active Directory zarejestrowanych urządzeń z listy](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Włączanie dostępu warunkowego
Szczegółowe instrukcje dotyczące realizacji tego scenariusza są dostępne w ramach [Konfigurowanie lokalnego dostępu warunkowego przy użyciu usługi Azure Active Directory rejestracji urządzenia](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="the-writeback-checkbox-is-still-disabled"></a>Zaznacz pole wyboru funkcji zapisywania zwrotnego nadal jest wyłączona.
Jeśli pole wyboru, aby funkcja zapisywania zwrotnego urządzeń nie jest włączone, nawet jeśli powyższe kroki zostały wykonane, poniższe kroki przeprowadzi Cię przez co Kreator instalacji sprawdza przed pole jest włączone.

Najważniejsze pierwszy:

* Lasu, gdy urządzenia są obecne musi mieć schemat lasu uaktualnione do poziomu systemu Windows 2012 R2, aby obiekt urządzenia i skojarzonych z nimi atrybutów są obecne.
* Jeśli Kreator instalacji jest już uruchomiona, wszelkie zmiany nie można wykryć. W takim przypadku można zakończyć działanie Kreatora instalacji i uruchom go ponownie.
* Upewnij się, że konto, którego należy podać w skrypcie inicjowania jest faktycznie właściwy użytkownik używane przez łącznik usługi Active Directory. Aby to sprawdzić, wykonaj następujące kroki:
  * Z start menu, otwórz **usługi synchronizacji**.
  * Otwórz **łączników** kartę.
  * Możesz znaleźć odpowiedniego łącznika z typem Active Directory Domain Services i wybierz ją.
  * W obszarze **akcje**, wybierz opcję **właściwości**.
  * Przejdź do **nawiązać połączenie z lasu usługi Active Directory**. Sprawdź, czy domena i nazwa użytkownika określona w tym dopasowania ekranu konto przekazane do skryptu.
    ![Konta łącznika w Menedżerze usługi synchronizacji](./media/how-to-connect-device-writeback/connectoraccount.png)

Weryfikowanie konfiguracji w usłudze Active Directory:

* Sprawdź, czy usługi rejestracji urządzeń znajduje się w poniższej lokalizacji (CN = DeviceRegistrationService, CN usługi rejestracji urządzeń, CN = konfiguracji rejestracji urządzenia, CN = Services, CN = Configuration) w kontekście nazewnictwa konfiguracji.

![Rozwiązywanie problemów-DeviceRegistrationService w przestrzeni nazw konfiguracji](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Sprawdź, czy istnieje tylko jeden obiekt konfiguracji, wyszukując konfiguracji przestrzeni nazw. Jeśli istnieje więcej niż jeden, Usuń duplikat.

![Rozwiązywanie problemów, wyszukiwanie zduplikowanych obiektów](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Upewnij się, atrybut msDS-DeviceLocation jest obecny i ma wartość na obiekt usługi rejestracji urządzeń. Wyszukiwanie tej lokalizacji i upewnij się, że nie jest obecny przy użyciu objectType msDS-DeviceContainer.

![Troubleshoot, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Rozwiązywanie problemów, klasa obiektu RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Sprawdź, czy konto używane przez łącznik usługi Active Directory ma wymagane uprawnienia w kontenerze zarejestrowane urządzenia, znaleziono w poprzednim kroku. Jest to oczekiwane uprawnienia do tego kontenera:

![Rozwiązywanie problemów, sprawdź uprawnienia do kontenera](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Sprawdź konto usługi Active Directory ma uprawnienia do CN konfiguracji rejestracji urządzenia, CN = Services, CN = obiekt konfiguracji.

![Rozwiązywanie problemów, sprawdź uprawnienia do konfiguracji rejestracji urządzenia](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Dodatkowe informacje
* [Zarządzanie ryzykiem przy użyciu dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Konfigurowanie lokalnego dostępu warunkowego przy użyciu usługi Azure Active Directory rejestracji urządzenia](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

