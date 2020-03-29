---
title: Uwierzytelnianie usługi Azure AD dla storsimple 8000 w Menedżerze urządzeń
description: W tym artykule wyjaśniono, jak używać uwierzytelniania opartego na usłudze AAD dla usługi, generować nowy klucz rejestracji i przeprowadzać ręczną rejestrację urządzeń.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b16132c24d35ee2c9902fa2b21c44416d8376b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470908"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Używanie nowego uwierzytelniania dla storsimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Usługa StorSimple Device Manager działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Do tej pory usługa StorSimple Device Manager używała usługi kontroli dostępu (ACS) do uwierzytelniania usługi na urządzeniu StorSimple. Mechanizm usługi ACS zostanie wkrótce przestarzały i zastąpiony uwierzytelnianiem usługi Azure Active Directory (AAD). Aby uzyskać więcej informacji, przejdź do następujących anonsów dotyczących umorzyniania usługi ACS i korzystania z uwierzytelniania usługi AAD.

- [Przyszłością usługi Azure ACS jest usługa Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Nadchodzące zmiany w usłudze Microsoft Access Control](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

W tym artykule opisano szczegóły uwierzytelniania usługi AAD i skojarzonego nowego klucza rejestracji usługi oraz modyfikacje reguł zapory w odniesieniu do urządzeń StorSimple. Informacje zawarte w tym artykule mają zastosowanie tylko do urządzeń z serii StorSimple 8000.

Uwierzytelnianie AAD występuje na urządzeniu storsimple serii 8000 z aktualizacją 5 lub nowszą. Ze względu na wprowadzenie uwierzytelniania AAD występują zmiany w:

- Wzorce adresów URL dla reguł zapory.
- Klucz rejestracji usługi.

Zmiany te zostały szczegółowo omówione w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL w przypadku uwierzytelniania AAD

Aby upewnić się, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy muszą dołączyć nowe adresy URL uwierzytelniania do reguł zapory.

Jeśli używasz serii StorSimple 8000, upewnij się, że następujący adres URL znajduje się w regułach zapory:

| Wzorzec adresu URL                         | Chmura | Komponent/funkcjonalność         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure — publiczna |Usługa uwierzytelniania AAD      |
| `https://login.microsoftonline.us` | US Government |Usługa uwierzytelniania AAD      |

Aby uzyskać pełną listę wzorców adresów URL dla urządzeń z serii StorSimple 8000, przejdź do [wzorców adresów URL dla reguł zapory](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory poza datą wycofania, a na urządzeniu jest uruchomiona aktualizacja 5, użytkownicy zobaczą alert adresu URL. Użytkownicy muszą dołączyć nowy adres URL uwierzytelniania. Jeśli na urządzeniu jest uruchomiona wersja przed aktualizacją 5, użytkownicy zobaczą alert pulsu. W każdym przypadku urządzenie StorSimple nie może uwierzytelnić się za pomocą usługi, a usługa nie może komunikować się z urządzeniem.

## <a name="device-version-and-authentication-changes"></a>Zmiany w wersji urządzenia i uwierzytelnianiu

Jeśli używasz urządzenia z serii StorSimple 8000, użyj poniższej tabeli, aby określić, jakie działania należy wykonać na podstawie uruchomionej wersji oprogramowania urządzenia.

| Jeśli urządzenie jest uruchomione| Podejmij następujące działania                                    |
|--------------------------|------------------------|
| Aktualizacja 5 lub nowsza, a urządzenie jest w trybie offline. <br> Zostanie wyświetlony alert, że adres URL nie jest umieszczony na białej liście.|1. Zmodyfikuj reguły zapory, aby uwzględnić adres URL uwierzytelniania. Zobacz [adresy URL uwierzytelniania](#url-changes-for-aad-authentication).<br>2. [Pobierz klucz rejestracyjny AAD z usługi](#aad-based-registration-keys).<br>3. [Połącz się z interfejsem programu Windows PowerShell urządzenia storsimple serii 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. `Redo-DeviceRegistration` Użyj polecenia cmdlet, aby zarejestrować urządzenie za pośrednictwem programu Windows PowerShell. Podaj klucz, który otrzymałeś w poprzednim kroku.|
| Aktualizacja 5 lub nowsza i urządzenie w trybie online.| Nie jest wymagana żadna akcja.                                       |
| Aktualizacja 4 lub wcześniejsza, a urządzenie jest w trybie offline. |1. Zmodyfikuj reguły zapory, aby uwzględnić adres URL uwierzytelniania.<br>2. [Pobierz aktualizację 5 za pośrednictwem serwera katalogowego](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Zastosuj aktualizację 5 za pomocą metody poprawki](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Pobierz klucz rejestracyjny AAD z usługi](#aad-based-registration-keys).<br>5. [Połącz się z interfejsem programu Windows PowerShell urządzenia storsimple serii 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. `Redo-DeviceRegistration` Użyj polecenia cmdlet, aby zarejestrować urządzenie za pośrednictwem programu Windows PowerShell. Podaj klucz, który otrzymałeś w poprzednim kroku.|
| Aktualizacja 4 lub wcześniejsza, a urządzenie jest w trybie online. |Zmodyfikuj reguły zapory, aby uwzględnić adres URL uwierzytelniania.<br> Zainstaluj aktualizację 5 za pośrednictwem witryny Azure portal.              |
| Przywrócenie ustawień fabrycznych do wersji przed aktualizacją 5.      |Portal pokazuje klucz rejestracji oparty na uad, gdy urządzenie jest uruchomione starsze oprogramowanie. Wykonaj kroki opisane w poprzednim scenariuszu, gdy na urządzeniu jest uruchomiona aktualizacja 4 lub wcześniej.              |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji oparte na AAD

Począwszy od aktualizacji 5 dla urządzeń z serii StorSimple 8000, używane są nowe klucze rejestracyjne oparte na UAD. Za pomocą kluczy rejestracyjnych można zarejestrować usługę StorSimple Device Manager na urządzeniu.

Nie można używać nowych kluczy rejestracji usługi AAD, jeśli używasz urządzenia z serii StorSimple 8000 z aktualizacją 4 lub starszą (w tym starsze urządzenie jest teraz aktywowane).
W tym scenariuszu należy ponownie wygenerować klucz rejestracji usługi. Po wygenerowaniu klucza nowy klucz jest używany do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji AAD wygasa po 3 dniach.
- Klucze rejestracji usługi AAD działają tylko z urządzeniami z serii StorSimple 8000 z aktualizacją 5 lub nowszą.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiednie klucze rejestracji usługi ACS.

Wykonaj następujące kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **Menedżerze urządzeń StorSimple**przejdź do **klawiszy** **zarządzania &gt; ** . Za pomocą paska wyszukiwania można również wyszukać _klawisze_.
    
2. Kliknij **pozycję Generuj klucz**.

    ![Kliknij przycisk wygeneruj ponownie](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Starszy klucz nie będzie już działać.

    ![Potwierdź regenerację](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Jeśli tworzysz Urządzenie StorSimple Cloud w usłudze zarejestrowanej na urządzeniu z serii StorSimple 8000, nie wygeneruj klucza rejestracji podczas tworzenia. Poczekaj na zakończenie tworzenia, a następnie wygeneruj klucz rejestracji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wdrażaniu urządzenia z serii StorSimple 8000.](storsimple-8000-deployment-walkthrough-u2.md)

