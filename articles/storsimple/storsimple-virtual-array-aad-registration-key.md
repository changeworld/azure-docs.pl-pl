---
title: Nowe uwierzytelnianie dla macierzy wirtualnych StorSimple | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak używać uwierzytelniania usługi AAD na podstawie usługi, wygeneruj nowy klucz rejestracji i przeprowadzenia ręcznej rejestracji urządzeń.
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
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: 080f49ca1078858462264f229e9acfee6fad17d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387656"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Nowe uwierzytelnianie dla usługi StorSimple

## <a name="overview"></a>Omówienie

Usługa Menedżer urządzeń StorSimple działa na platformie Microsoft Azure i łączy wiele macierzy wirtualnej StorSimple. Do tej pory, usługi Menedżer urządzeń StorSimple został użyty do uwierzytelnienia usługi do urządzenia StorSimple, usługi Access Control service (ACS). Mechanizm ACS będzie niedługo przestarzała i zastąpione przez uwierzytelnianie usługi Azure Active Directory (AAD).

Informacje zawarte w tym artykule dotyczy zarówno StorSimple 1200 serii macierzy wirtualnych tylko. W tym artykule opisano szczegóły uwierzytelniania w usłudze AAD i skojarzone nowy klucz rejestracji usługi i modyfikacji reguły zapory, jeśli ma zastosowanie do urządzeń StorSimple.

Uwierzytelnianie usługi AAD odbywa się w macierzach wirtualnych StorSimple (model 1200) z aktualizacją Update 1 lub nowszym.

Z powodu wprowadzenia uwierzytelnianie w usłudze AAD zmian w:

- Wzorce adresów URL dla reguł zapory.
- Klucz rejestracji usługi.

Zmiany te omówiono szczegółowo w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL dla uwierzytelniania w usłudze AAD

Aby upewnić się, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy musi zawierać nowe adresy URL uwierzytelniania w ich reguł zapory.

Jeśli używasz rozwiązania StorSimple Virtual Array, upewnij się, że następujący adres URL znajduje się w regułach zapory:

| Wzorzec URL                         | Chmura | Składnik/funkcji         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |Usługa uwierzytelniania usługi AAD      |
| `https://login.microsoftonline.us` | Rząd USA |Usługa uwierzytelniania usługi AAD      |

Aby uzyskać pełną listę adresów URL wzorce dla macierzy wirtualnych StorSimple, przejdź do [wzorce adresów URL dla reguł zapory](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory po dacie zakończenia obsługi, widzą alert krytyczny, że ich urządzenia StorSimple nie mogło przeprowadzić uwierzytelniania w usłudze. Usługa nie będzie mogła nawiązać połączenia z urządzeniem. Użytkownicy widzą ten alert, muszą zawierać nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji na temat alertu, przejdź do [Użyj alerty, aby monitorować urządzenie StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Zmiany wersji i uwierzytelnianie urządzeń

Jeśli używasz rozwiązania StorSimple Virtual Array, skorzystaj z poniższej tabeli, aby ustalić, jakie działania muszą być oparte na wersji oprogramowania urządzenia, które są uruchomione.

| Jeśli na urządzeniu jest uruchomiona  | Wykonaj następującą akcję                                    |
|----------------------------|--------------------------------------------------------------|
| Aktualizacja wersji 1.0 lub nowszej i jest w trybie offline. <br> Zostanie wyświetlony alert, że adres URL nie jest umieszczona na białej liście.| 1. Modyfikowanie reguły zapory, aby zawierały adres URL uwierzytelniania. Zobacz [adresy URL uwierzytelniania](#url-changes-for-aad-authentication). <br> 2. [Pobieranie klucza rejestracji usługi AAD z usługi](#aad-based-registration-keys). <br> 3. Wykonaj kroki od 1 do 5, aby [nawiązywanie połączenia z interfejsu programu Windows PowerShell macierzy wirtualnej](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Użyj `Invoke-HcsReRegister` polecenia cmdlet, aby zarejestrować urządzenia za pomocą programu Windows PowerShell. Podaj klucz uzyskany w poprzednim kroku.|
| Update 1.0 lub nowszej, a urządzenie jest w trybie online.| Nie jest wymagana żadna akcja.                                       |
| Update 0.6 lub wcześniejszej i urządzenie jest w trybie offline. | 1. [Pobierz aktualizację 1.0 za pośrednictwem serwera katalogu](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Stosowanie aktualizacji 1.0 przy użyciu lokalnego Interfejsu w przeglądarce](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Pobieranie klucza rejestracji usługi AAD z usługi](#aad-based-registration-keys). <br>4. Wykonaj kroki od 1 do 5, aby [nawiązywanie połączenia z interfejsu programu Windows PowerShell macierzy wirtualnej](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Użyj `Invoke-HcsReRegister` polecenia cmdlet, aby zarejestrować urządzenia za pomocą programu Windows PowerShell. Podaj klucz uzyskany w poprzednim kroku.|
| Update 0.6 lub wcześniejszej i urządzenie jest w trybie online | Modyfikowanie reguły zapory, aby zawierały adres URL uwierzytelniania.<br> Zainstaluj aktualizację 1.0 za pośrednictwem witryny Azure portal. |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji opartych na usłudze AAD

Począwszy od aktualizacji 1.0 dla macierzy wirtualnych StorSimple, nowej rejestracji opartych na usłudze AAD używane klucze. Klucze rejestracji umożliwia rejestrowanie usługi Menedżer urządzeń StorSimple z urządzeniem.

Nie można użyć nowych kluczy rejestracji usługi AAD, jeśli używasz macierze wirtualne StorSimple z aktualizacją Update 0.6 lub wcześniejszej. Musisz ponownie wygenerować klucz rejestracji usługi. Po wygenerowaniu klucza, nowy klucz jest używany do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji usługi AAD wygasa po 3 dniach.
- Pracy klucze rejestracji usługi AAD tylko z serii StorSimple 1200 wirtualnego tablic uruchomionej Update 1 lub nowszej. Klucz rejestracji usługi AAD z urządzenia z serii StorSimple 8000, nie będzie działać.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiadające klucze rejestracji usługi ACS.

Wykonaj poniższe kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **Menedżer urządzeń StorSimple**, przejdź do **zarządzania &gt;**  **klucze**.
    
    ![Przejdź do kluczy](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kliknij przycisk **Wygeneruj klucz**.

    ![Kliknij przycisk Wygeneruj ponownie klucz](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Starszy klucz nie będzie działać.

    ![Upewnij się, Wygeneruj ponownie klucz](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o sposobie wdrażania [macierzy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
