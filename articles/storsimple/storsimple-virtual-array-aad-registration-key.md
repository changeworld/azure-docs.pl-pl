---
title: Nowe uwierzytelniania dla tablic wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak używać uwierzytelniania w usłudze AAD, na podstawie usługi, wygenerować nowy klucz rejestracji i dokonać ręcznej rejestracji urządzeń.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 8d033cc09de8e115324067d7bbdf052751730d63
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28030954"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Użyj nowego uwierzytelniania dla Twojego urządzenia StorSimple

## <a name="overview"></a>Przegląd

Usługa Menedżera urządzeń StorSimple działa na platformie Microsoft Azure i łączy wiele tablic wirtualne StorSimple. Data, usługi Menedżer StorSimple urządzenia został użyty do uwierzytelnienia usługi dla urządzenia StorSimple, usługi kontroli dostępu (ACS). Mechanizm ACS będzie niedługo przestarzała i zastępuje uwierzytelniania usługi Azure Active Directory (AAD).

Informacje zawarte w tym artykule ma zastosowanie do obu StorSimple 1200 serii wirtualnego tablic tylko. W tym artykule opisano uwierzytelniania w usłudze AAD skojarzony nowy klucz rejestracji usługi i modyfikacji reguły zapory, co dotyczy urządzeń StorSimple.

Uwierzytelnianie usługi AAD odbywa się w tablicach wirtualnego StorSimple (model 1200) z aktualizacji 1 lub nowszą.

Z powodu wprowadzenia uwierzytelniania w usłudze AAD zmian w:

- Adres URL wzorce dla reguł zapory.
- Klucz rejestracji usługi.

Zmiany te omówiono szczegółowo w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL dla uwierzytelniania w usłudze AAD

Aby upewnić się, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy muszą zawrzeć nowe adresy URL uwierzytelniania w ich reguł zapory.

Jeśli przy użyciu tablicy wirtualnego StorSimple, upewnij się, czy następujący adres URL jest uwzględniona w reguły zapory:

| Wzorzec URL                         | Chmura | Składnik/funkcji         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |Usługa uwierzytelniania usługi AAD      |
| `https://login.microsoftonline.us` | Rząd USA |Usługa uwierzytelniania usługi AAD      |

Aby uzyskać pełną listę adresów URL wzorce dla tablic wirtualnego StorSimple, przejdź do [wzorców adresów URL dla reguł zapory](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Jeśli adres URL uwierzytelniania nie jest uwzględniony w zasadach zapory poza Data zaprzestania, użytkownicy widzą alert krytyczny, który ich urządzenia StorSimple nie można uwierzytelnić w usłudze. Usługa nie będzie mogła nawiązać połączenia z urządzeniem. Użytkownicy widzą ten alert, muszą zawierać nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji o alercie, przejdź do [Użyj alertów do monitorowania urządzenia StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Zmiany wersji i uwierzytelnianie urządzenia

Jeśli używany jest tablicą wirtualnego StorSimple, skorzystaj z poniższej tabeli, aby ustalić, jakie działania należy podjąć oparta na wersji oprogramowania urządzenia, którym jest uruchomiony.

| Jeśli urządzenie korzysta  | Wykonaj następującą akcję                                    |
|----------------------------|--------------------------------------------------------------|
| Aktualizacja z wersji 1.0 lub nowszej i jest w trybie offline. <br> Zostanie wyświetlony alert, że adres URL nie jest białej.| Modyfikowanie reguły zapory w celu uwzględnienia adres URL uwierzytelniania. Zobacz [adresów URL uwierzytelniania](#url-changes-for-aad-authentication). |
| Aktualizacji wersji 1.0 lub nowszej, a urządzenie jest w trybie online.| Nie jest wymagana żadna akcja.                                       |
| Aktualizacja 0,6 lub starszym i urządzenie jest w trybie offline. | [Pobierz aktualizację w wersji 1.0 za pośrednictwem serwera katalogu](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>[Zastosuj Update 1.0 za pośrednictwem lokalnego interfejsu użytkownika sieci web](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix). <br> [Pobierz klucz rejestracji usługi AAD z usługi](#aad-based-registration-keys). <br> Wykonaj kroki 1-5, aby [Połącz z interfejsu programu Windows PowerShell wirtualnego tablicy](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> Użyj `Invoke-HcsReRegister` polecenia cmdlet, aby zarejestrować urządzenia za pomocą programu Windows PowerShell. Określ klucz uzyskanego w poprzednim kroku.|
| Aktualizacja 0,6 lub starszym i urządzenie jest w trybie online | Modyfikowanie reguły zapory w celu uwzględnienia adres URL uwierzytelniania.<br> Zainstaluj Update 1.0 za pośrednictwem portalu Azure. |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji opartych na usłudze AAD

Początek Update 1.0 dla tablic wirtualnego StorSimple, nowej rejestracji na podstawie AAD klucze są używane. Klucze rejestracji jest używany do rejestrowania usługi Menedżer StorSimple urządzenia z urządzeniem.

Nie można używać nowych kluczy rejestracji usługi AAD, jeśli używasz tablice wirtualnego StorSimple, uruchomioną aktualizacją 0,6 lub starszym. Musisz ponownie wygenerować klucz rejestracji usługi. Po wygenerowaniu klucza nowy klucz służy do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji usługi AAD wygasa po 3 dni.
- Praca klucze rejestracji usługi AAD tylko z serii StorSimple 1200 wirtualnego stałych uruchomionych Update 1 lub nowszej. Klucz rejestracji usługi AAD z serii StorSimple 8000 urządzenia nie będzie działać.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiadające im klucze rejestracji ACS.

Wykonaj poniższe kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **Menedżera urządzeń StorSimple**, przejdź do **zarządzania &gt;**  **klucze**.
    
    ![Przejdź do kluczy](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kliknij przycisk **generowanie klucza**.

    ![Kliknij przycisk regenerate](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Starsze klucz nie działa.

    ![Potwierdź regenerate](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o sposobie wdrażania [tablicy wirtualnego StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
