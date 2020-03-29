---
title: Nowe uwierzytelnianie dla tablic wirtualnych StorSimple
description: W tym artykule wyjaśniono, jak używać uwierzytelniania opartego na usłudze AAD dla usługi, generować nowy klucz rejestracji i przeprowadzać ręczną rejestrację urządzeń.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 89f367e866c1a794f4359c76b8b8a8a9cfefd50d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273808"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Używanie nowego uwierzytelniania dla storsimple

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Usługa StorSimple Device Manager działa na platformie Microsoft Azure i łączy się z wieloma macierzami wirtualnymi StorSimple. Do tej pory usługa StorSimple Device Manager używała usługi kontroli dostępu (ACS) do uwierzytelniania usługi na urządzeniu StorSimple. Mechanizm usługi ACS zostanie wkrótce przestarzały i zastąpiony uwierzytelnianiem usługi Azure Active Directory (AAD).

Informacje zawarte w tym artykule mają zastosowanie tylko do tablic wirtualnych StorSimple serii 1200. W tym artykule opisano szczegóły uwierzytelniania usługi AAD i skojarzonego nowego klucza rejestracji usługi oraz modyfikacje reguł zapory w odniesieniu do urządzeń StorSimple.

Uwierzytelnianie AAD występuje w StorSimple tablice wirtualne (model 1200) z aktualizacją 1 lub nowszą.

Ze względu na wprowadzenie uwierzytelniania AAD występują zmiany w:

- Wzorce adresów URL dla reguł zapory.
- Klucz rejestracji usługi.

Zmiany te zostały szczegółowo omówione w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL w przypadku uwierzytelniania AAD

Aby upewnić się, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy muszą dołączyć nowe adresy URL uwierzytelniania do reguł zapory.

Jeśli używasz tablicy wirtualnej StorSimple, upewnij się, że w regułach zapory znajduje się następujący adres URL:

| Wzorzec adresu URL                         | Chmura | Komponent/funkcjonalność         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure — publiczna |Usługa uwierzytelniania AAD      |
| `https://login.microsoftonline.us` | US Government |Usługa uwierzytelniania AAD      |

Aby uzyskać pełną listę wzorców adresów URL dla tablic wirtualnych StorSimple, przejdź do [wzorców adresów URL dla reguł zapory](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory poza datą wycofania, użytkownicy zobaczą alert krytyczny, że ich urządzenie StorSimple nie może uwierzytelnić się za pomocą usługi. Usługa nie będzie mogła komunikować się z urządzeniem. Jeśli użytkownicy zobaczą ten alert, muszą dołączyć nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji na temat alertu, przejdź do [witryny Użyj alertów do monitorowania urządzenia StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Zmiany w wersji urządzenia i uwierzytelnianiu

Jeśli używasz Tablicy wirtualnej StorSimple, użyj poniższej tabeli, aby określić, jakie działania należy wykonać na podstawie używanej wersji oprogramowania urządzenia.

| Jeśli urządzenie jest uruchomione  | Podejmij następujące działania                                    |
|----------------------------|--------------------------------------------------------------|
| Aktualizacja 1.0 lub nowsza i jest w trybie offline. <br> Zostanie wyświetlony alert, że adres URL nie jest umieszczony na białej liście.| 1. Zmodyfikuj reguły zapory, aby uwzględnić adres URL uwierzytelniania. Zobacz [adresy URL uwierzytelniania](#url-changes-for-aad-authentication). <br> 2. [Pobierz klucz rejestracyjny AAD z usługi](#aad-based-registration-keys). <br> 3. Wykonaj kroki 1-5, [aby połączyć się z interfejsem programu Windows PowerShell tablicy wirtualnej](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. `Invoke-HcsReRegister` Użyj polecenia cmdlet, aby zarejestrować urządzenie za pośrednictwem programu Windows PowerShell. Podaj klucz, który otrzymałeś w poprzednim kroku.|
| Aktualizacja 1.0 lub nowsza, a urządzenie jest w trybie online.| Nie jest wymagana żadna akcja.                                       |
| Aktualizacja 0.6 lub wcześniejsza, a urządzenie jest w trybie offline. | 1. [Pobierz aktualizację 1.0 za pośrednictwem serwera katalogowego](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Zastosuj aktualizację 1.0 za pośrednictwem lokalnego interfejsu użytkownika sieci](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Pobierz klucz rejestracyjny AAD z usługi](#aad-based-registration-keys). <br>4. Wykonaj kroki 1-5, [aby połączyć się z interfejsem programu Windows PowerShell tablicy wirtualnej](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. `Invoke-HcsReRegister` Użyj polecenia cmdlet, aby zarejestrować urządzenie za pośrednictwem programu Windows PowerShell. Podaj klucz, który otrzymałeś w poprzednim kroku.|
| Aktualizacja 0.6 lub wcześniejsza, a urządzenie jest w trybie online | Zmodyfikuj reguły zapory, aby uwzględnić adres URL uwierzytelniania.<br> Zainstaluj aktualizację 1.0 za pośrednictwem witryny Azure portal. |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji oparte na AAD

Począwszy od aktualizacji 1.0 dla tablic wirtualnych StorSimple, używane są nowe klucze rejestracji oparte na usłudze AAD. Za pomocą kluczy rejestracyjnych można zarejestrować usługę StorSimple Device Manager na urządzeniu.

Nie można używać nowych kluczy rejestracji usługi AAD, jeśli używasz tablic wirtualnych StorSimple z aktualizacją 0.6 lub wcześniejszą. Należy ponownie wygenerować klucz rejestracji usługi. Po wygenerowaniu klucza nowy klucz jest używany do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji AAD wygasa po 3 dniach.
- Klucze rejestracji usługi AAD działają tylko z macierzami wirtualnymi serii StorSimple 1200 z aktualizacją 1 lub nowszą. Klucz rejestracji usługi AAD z urządzenia storsimple serii 8000 nie będzie działać.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiednie klucze rejestracji usługi ACS.

Wykonaj następujące kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **Menedżerze urządzeń StorSimple**przejdź do **klawiszy** **zarządzania &gt; ** .
    
    ![Przejdź do klawiszy](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kliknij **pozycję Generuj klucz**.

    ![Kliknij przycisk wygeneruj ponownie](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Starszy klucz już nie działa.

    ![Potwierdź regenerację](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wdrażaniu tablicy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
