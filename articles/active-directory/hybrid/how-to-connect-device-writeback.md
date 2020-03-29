---
title: 'Usługa Azure AD Connect: włączanie zapisywania zwrotnego na urządzeniach | Dokumenty firmy Microsoft'
description: W tym dokumencie opisano, jak włączyć zapisywanie zwrotne urządzenia przy użyciu usługi Azure AD Connect
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
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109498"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Usługa Azure AD Connect: włączanie zapisywania zwrotnego na urządzeniach
> [!NOTE]
> Subskrypcja usługi Azure AD Premium jest wymagana do zapisywania zwrotnego urządzenia.
> 
> 

Poniższa dokumentacja zawiera informacje dotyczące włączania funkcji zapisywania zwrotnego urządzenia w usłudze Azure AD Connect. Zapis zwrotny urządzenia jest używany w następujących scenariuszach:

* Włączanie [funkcji Windows Hello dla firm przy użyciu wdrożenia zaufania certyfikatów hybrydowych](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Włącz dostęp warunkowy na podstawie urządzeń do aplikacji chronionych usługi ADFS (2012 R2 lub nowszych) (relacje zaufania jednostki uzależnionej).

Zapewnia to dodatkowe bezpieczeństwo i pewność, że dostęp do aplikacji jest przyznawany tylko zaufanym urządzeniom. Aby uzyskać więcej informacji na temat dostępu warunkowego, zobacz Zarządzanie ryzykiem za [pomocą dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) i [konfigurowanie lokalnego dostępu warunkowego przy użyciu rejestracji urządzeń usługi Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Urządzenia muszą znajdować się w tym samym lesie co użytkownicy. Ponieważ urządzenia muszą być zapisywane z powrotem do jednego lasu, ta funkcja nie obsługuje obecnie wdrożenia z wieloma lasami użytkowników.</li>
> <li>Do lokalnego lasu usługi Active Directory można dodać tylko jeden obiekt konfiguracji rejestracji urządzenia. Ta funkcja nie jest zgodna z topologią, w której lokalna usługa Active Directory jest synchronizowana z wieloma katalogami usługi Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Część 1: Instalowanie usługi Azure AD Connect
Zainstaluj usługę Azure AD Connect przy użyciu ustawień niestandardowych lub ekspresowych. Firma Microsoft zaleca, aby rozpocząć od wszystkich użytkowników i grup pomyślnie zsynchronizowanych przed włączeniem zapisywania zwrotnego urządzenia.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Część 2: Włącz zapisywanie zwrotne urządzenia w usłudze Azure AD Connect
1. Ponownie uruchom kreatora instalacji. Wybierz **pozycję Konfiguruj opcje urządzenia** na stronie Zadania dodatkowe i kliknij przycisk **Dalej**. 

    ![Konfigurowanie opcji urządzenia](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Nowe opcje konfigurowania urządzenia są dostępne tylko w wersji 1.1.819.0 i nowszej.

2. Na stronie opcji urządzenia wybierz pozycję **Konfiguruj storament urządzenia .** Opcja **Wyłączanie stornia zwrotnego urządzenia** nie będzie dostępna, dopóki nie zostanie włączone zapisywanie zwrotne urządzenia. Kliknij **przycisk Dalej,** aby przejść do następnej strony kreatora.
    ![Wybór operacji urządzenia](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na stronie stornia zwrotnego zostanie wyświetlona dostarczona domena jako domyślny las stornia zwrotnego urządzenia.
   ![Las docelowy zapisu zwrotnego na urządzeniu instalacji niestandardowej](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Strona kontenera urządzenia** umożliwia przygotowanie usługi active directory przy użyciu jednej z dwóch dostępnych opcji:

    a. **Podaj poświadczenia administratora przedsiębiorstwa:** Jeśli poświadczenia administratora przedsiębiorstwa są podane dla lasu, w którym urządzenia muszą być zapisywane, usługa Azure AD Connect automatycznie przygotuje las podczas konfiguracji stornia zwrotnego urządzenia.

    b. **Pobierz skrypt programu PowerShell:** Usługa Azure AD Connect automatycznie generuje skrypt programu PowerShell, który może przygotować usługę active directory do zapisu urządzenia. W przypadku, gdy poświadczenia administratora przedsiębiorstwa nie mogą być dostarczone w usłudze Azure AD Connect, zaleca się pobranie skryptu programu PowerShell. Przekaż pobrany skrypt programu PowerShell **CreateDeviceContainer.psq** administratorowi przedsiębiorstwa lasu, do którego urządzenia będą zapisywane.
    ![Przygotowywanie lasu usługi active directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Następujące operacje są wykonywane w celu przygotowania lasu usługi active directory:
    * Jeśli jeszcze nie istnieją, tworzy i konfiguruje nowe kontenery i obiekty w obszarze CN=Konfiguracja rejestracji urządzeń,CN=Usługi,CN=Konfiguracja,[forest-dn].
    * Jeśli jeszcze nie istnieją, tworzy i konfiguruje nowe kontenery i obiekty w obszarze CN=RegisteredDevices,[domain-dn]. Obiekty urządzenia zostaną utworzone w tym kontenerze.
    * Ustawia niezbędne uprawnienia na koncie usługi Azure AD Connector, aby zarządzać urządzeniami w usłudze Active Directory.
    * Tylko musi działać na jednym lesie, nawet jeśli usługa Azure AD Connect jest instalowana w wielu lasach.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Sprawdź, czy urządzenia są synchronizowane z usługą Active Directory
Zapisywanie zwrotne urządzenia powinno teraz działać poprawnie. Należy pamiętać, że może upłynąć do 3 godzin dla obiektów urządzenia, które mają być zapisywane z powrotem do usługi AD.  Aby sprawdzić, czy urządzenia są synchronizowane prawidłowo, wykonaj następujące czynności po zakończeniu reguł synchronizacji:

1. Uruchom Centrum administracyjne usługi Active Directory.
2. Rozwiń Zarejestrowanychurządzenia w domenie, która jest sfederowana.

   ![Zarejestrowane urządzenia Centrum administracyjne usługi Active Directory](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Zostaną tam wymienione aktualne zarejestrowane urządzenia.

   ![Lista zarejestrowanych urządzeń Centrum administracyjnego usługi Active Directory](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Włączanie dostępu warunkowego
Szczegółowe instrukcje umożliwiające włączenie tego scenariusza są dostępne w obszarze [Konfigurowanie lokalnego dostępu warunkowego przy użyciu rejestracji urządzeń usługi Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="the-writeback-checkbox-is-still-disabled"></a>Pole wyboru stornia jest nadal wyłączone
Jeśli pole wyboru dotyczące zapisywania zwrotnego urządzenia nie jest włączone, nawet jeśli poniżej postępujesz, poniższe kroki poprowadzą Cię przez to, co kreator instalacji weryfikuje przed włączeniem tego pola.

Po pierwsze:

* Las, w którym obecne są urządzenia, musi mieć uaktualniony schemat lasu do poziomu systemu Windows 2012 R2, tak aby obiekt urządzenia i skojarzone atrybuty były obecne .
* Jeśli kreator instalacji jest już uruchomiony, żadne zmiany nie zostaną wykryte. W takim przypadku należy ukończyć kreatora instalacji i uruchomić go ponownie.
* Upewnij się, że konto, które podasz w skrypcie inicjowania, jest w rzeczywistości poprawnym użytkownikiem używanym przez łącznik usługi Active Directory. Aby to sprawdzić, wykonaj następujące kroki:
  * Z menu Start otwórz usługę **synchronizacji**.
  * Otwórz kartę **Łączniki.**
  * Znajdź łącznik z typem Usługi domenowe Active Directory i wybierz go.
  * W obszarze **Akcje**wybierz pozycję **Właściwości**.
  * Przejdź do **funkcji Połącz z lasem usługi Active Directory**. Sprawdź, czy domena i nazwa użytkownika określone na tym ekranie są zgodne z kontem dostarczonym do skryptu.
    ![Konto łącznika w Menedżerze usługi synchronizacji](./media/how-to-connect-device-writeback/connectoraccount.png)

Sprawdź konfigurację w usłudze Active Directory:

* Sprawdź, czy usługa rejestracji urządzeń znajduje się w poniższej lokalizacji (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) w kontekście nazewnictwa konfiguracji.

![Rozwiązywanie problemów, usługa rejestracji urządzeń w obszarze nazw konfiguracji](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Sprawdź, czy istnieje tylko jeden obiekt konfiguracji, przeszukując obszar nazw konfiguracji. Jeśli istnieje więcej niż jeden, usuń duplikat.

![Rozwiązywanie problemów, wyszukiwanie zduplikowanych obiektów](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Na urządzenie rejestracji usługi obiektu, upewnij się, że atrybut msDS-DeviceLocation jest obecny i ma wartość. Odnoś tę lokalizację i upewnij się, że jest on obecny z obiektemType msDS-DeviceContainer.

![Rozwiązywanie problemów, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Rozwiązywanie problemów, klasa obiektu RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Sprawdź, czy konto używane przez łącznik usługi Active Directory ma wymagane uprawnienia do kontenera Zarejestrowane urządzenia znalezionego w poprzednim kroku. Jest to oczekiwane uprawnienia do tego kontenera:

![Rozwiązywanie problemów, weryfikowanie uprawnień do kontenera](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Sprawdź, czy konto usługi Active Directory ma uprawnienia do obiektu CN=Device Registration Configuration,CN=Services,CN=Configuration object.

![Rozwiązywanie problemów, weryfikowanie uprawnień w konfiguracji rejestracji urządzenia](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Dodatkowe informacje
* [Zarządzanie ryzykiem z dostępem warunkowym](../active-directory-conditional-access-azure-portal.md)
* [Konfigurowanie lokalnego dostępu warunkowego przy użyciu rejestracji urządzeń usługi Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

