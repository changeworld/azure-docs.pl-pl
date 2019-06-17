---
title: Nowe uwierzytelnianie dla usługi Menedżer StorSimple 8000 urządzeń na platformie Azure | Dokumentacja firmy Microsoft
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
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 01d36188c1684eae8303cb20ba0fd0c708ff91ba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309874"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Nowe uwierzytelnianie dla usługi StorSimple

## <a name="overview"></a>Omówienie

Usługa Menedżer urządzeń StorSimple działa na platformie Microsoft Azure i nawiązanie połączenia z wieloma urządzeniami StorSimple. Do tej pory, usługi Menedżer urządzeń StorSimple został użyty do uwierzytelnienia usługi do urządzenia StorSimple, usługi Access Control service (ACS). Mechanizm ACS będzie niedługo przestarzała i zastąpione przez uwierzytelnianie usługi Azure Active Directory (AAD). Aby uzyskać więcej informacji przejdź do następujących powiadomień dla obsługi usług ACS i korzystanie z uwierzytelniania usługi AAD.

- [Przyszłość platformy Azure ACS jest usługi Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Nadchodzących zmian do programu Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

W tym artykule opisano szczegóły uwierzytelniania w usłudze AAD i skojarzone nowy klucz rejestracji usługi i modyfikacji reguły zapory, jeśli ma zastosowanie do urządzeń StorSimple. Informacje zawarte w tym artykule dotyczy tylko urządzeń z serii StorSimple 8000.

Uwierzytelnianie usługi AAD odbywa się w urządzenia StorSimple 8000 series z aktualizacją Update 5 lub nowszej. Z powodu wprowadzenia uwierzytelnianie w usłudze AAD zmian w:

- Wzorce adresów URL dla reguł zapory.
- Klucz rejestracji usługi.

Zmiany te omówiono szczegółowo w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL dla uwierzytelniania w usłudze AAD

Aby upewnić się, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy musi zawierać nowe adresy URL uwierzytelniania w ich reguł zapory.

Jeśli z serii StorSimple 8000, upewnij się, że następujący adres URL znajduje się w regułach zapory:

| Wzorzec URL                         | Chmura | Składnik/funkcji         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |Usługa uwierzytelniania usługi AAD      |
| `https://login.microsoftonline.us` | Rząd USA |Usługa uwierzytelniania usługi AAD      |

Aby uzyskać pełną listę adresów URL wzorce dla urządzeń z serii StorSimple 8000, przejdź do [wzorce adresów URL dla reguł zapory](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Jeśli adres URL uwierzytelniania jest niedostępna w regułach zapory po dacie zakończenia obsługi, a na urządzeniu działa aktualizacja Update 5, widzą alert w postaci adresu URL. Użytkownicy muszą wykonać obejmują nowy adres URL uwierzytelniania. Jeśli urządzenie działa w wersji wcześniejszej niż aktualizacja Update 5, widzą alert dotyczący pulsu. W każdym przypadku urządzenia StorSimple nie mogą uwierzytelnić się w usłudze, a usługa nie mogła nawiązać połączenia z urządzeniem.

## <a name="device-version-and-authentication-changes"></a>Zmiany wersji i uwierzytelnianie urządzeń

Jeśli przy użyciu urządzenia serii StorSimple 8000, skorzystaj z poniższej tabeli, aby ustalić, jakie działania muszą być oparte na wersji oprogramowania urządzenia, które są uruchomione.

| Jeśli na urządzeniu jest uruchomiona| Wykonaj następującą akcję                                    |
|--------------------------|------------------------|
| Aktualizacji 5 lub nowszy, a urządzenie jest w trybie offline. <br> Zostanie wyświetlony alert, że adres URL nie jest umieszczona na białej liście.|1. Modyfikowanie reguły zapory, aby zawierały adres URL uwierzytelniania. Zobacz [adresy URL uwierzytelniania](#url-changes-for-aad-authentication).<br>2. [Pobieranie klucza rejestracji usługi AAD z usługi](#aad-based-registration-keys).<br>3. [Nawiązać połączenie z interfejsu programu Windows PowerShell urządzenia StorSimple 8000 series](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Użyj `Redo-DeviceRegistration` polecenia cmdlet, aby zarejestrować urządzenia za pomocą programu Windows PowerShell. Podaj klucz uzyskany w poprzednim kroku.|
| Aktualizacja 5 lub nowszy i urządzenie w tryb online.| Nie jest wymagana żadna akcja.                                       |
| Update 4 lub starszym, a urządzenie jest w trybie offline. |1. Modyfikowanie reguły zapory, aby zawierały adres URL uwierzytelniania.<br>2. [Pobieranie aktualizacji 5 za pośrednictwem serwera katalogu](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Stosowanie aktualizacji 5 za pomocą metody poprawkę](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Pobieranie klucza rejestracji usługi AAD z usługi](#aad-based-registration-keys).<br>5. [Nawiązać połączenie z interfejsu programu Windows PowerShell urządzenia StorSimple 8000 series](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Użyj `Redo-DeviceRegistration` polecenia cmdlet, aby zarejestrować urządzenia za pomocą programu Windows PowerShell. Podaj klucz uzyskany w poprzednim kroku.|
| Update 4 lub starszym, a urządzenie jest w trybie online. |Modyfikowanie reguły zapory, aby zawierały adres URL uwierzytelniania.<br> Instalowanie aktualizacji Update 5 za pomocą witryny Azure portal.              |
| Resetowanie do ustawień fabrycznych do wersji przed aktualizacją Update 5.      |Portal zawiera klucz rejestracji oparte na usłudze AAD, urządzeniu jest uruchomiona starsza oprogramowania. Wykonaj kroki opisane w poprzednim scenariuszu dla kiedy urządzenie działa aktualizacja 4 lub wersji wcześniejszych.              |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji opartych na usłudze AAD

Dla urządzeń z serii StorSimple 8000, nowej rejestracji opartych na usłudze AAD używane klucze, począwszy od aktualizacji Update 5. Klucze rejestracji umożliwia rejestrowanie usługi Menedżer urządzeń StorSimple z urządzeniem.

Nie można użyć nowych kluczy rejestracji usługi AAD, jeśli używasz urządzenia StorSimple 8000 series z aktualizacją Update 4 lub wersji wcześniejszych (w tym starsze urządzenia z systemem aktywowanego teraz).
W tym scenariuszu należy ponownie wygenerować klucz rejestracji usługi. Po wygenerowaniu klucza, nowy klucz jest używany do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji usługi AAD wygasa po 3 dniach.
- Klucze rejestracji usługi AAD, działają tylko z urządzeń serii StorSimple 8000 z aktualizacji 5 lub nowszym.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiadające klucze rejestracji usługi ACS.

Wykonaj poniższe kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **Menedżer urządzeń StorSimple**, przejdź do **zarządzania &gt;**  **klucze**. Umożliwia także na pasku wyszukiwania do wyszukania _klucze_.
    
2. Kliknij przycisk **Wygeneruj klucz**.

    ![Kliknij przycisk Wygeneruj ponownie klucz](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Starszy klucz nie będzie już działać.

    ![Upewnij się, Wygeneruj ponownie klucz](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > W przypadku tworzenia urządzenia StorSimple w chmurze w usłudze, zarejestrowane urządzenie StorSimple 8000 series nie Wygeneruj klucz rejestracji, w trakcie tworzenia. Poczekaj na ukończenie, a następnie wygenerować klucz rejestracji tworzenia.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o sposobie wdrażania [urządzenia StorSimple 8000 series](storsimple-8000-deployment-walkthrough-u2.md).

