---
title: Użyj uwierzytelniania nowej usługi Menedżer StorSimple 8000 urządzeń na platformie Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 37f44538d94ed78509bbcb09e726dc34a9e92e95
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28030947"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Użyj nowego uwierzytelniania dla Twojego urządzenia StorSimple

## <a name="overview"></a>Przegląd

Usługa Menedżera urządzeń StorSimple działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Data, usługi Menedżer StorSimple urządzenia został użyty do uwierzytelnienia usługi dla urządzenia StorSimple, usługi kontroli dostępu (ACS). Mechanizm ACS będzie niedługo przestarzała i zastępuje uwierzytelniania usługi Azure Active Directory (AAD). Aby uzyskać więcej informacji przejdź do następujących anonsów amortyzacja ACS i korzystanie z uwierzytelniania usługi AAD.

- [Przyszłość Azure ACS jest usługą Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Nadchodzące zmiany w usłudze kontroli dostępu firmy Microsoft](https://azure.microsoft.com/en-in/blog/acs-access-control-service-namespace-creation-restriction/)

W tym artykule opisano uwierzytelniania w usłudze AAD skojarzony nowy klucz rejestracji usługi i modyfikacji reguły zapory, co dotyczy urządzeń StorSimple. Informacje zawarte w tym artykule dotyczy tylko urządzeń z serii StorSimple 8000.

Uwierzytelnianie usługi AAD odbywa się w urządzeniu serii StorSimple 8000 uruchomioną aktualizacją 5 lub nowszy. Z powodu wprowadzenia uwierzytelniania w usłudze AAD zmian w:

- Adres URL wzorce dla reguł zapory.
- Klucz rejestracji usługi.

Zmiany te omówiono szczegółowo w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL dla uwierzytelniania w usłudze AAD

Aby upewnić się, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy muszą zawrzeć nowe adresy URL uwierzytelniania w ich reguł zapory.

Jeśli z serii StorSimple 8000, upewnij się, czy następujący adres URL jest uwzględniona w reguły zapory:

| Wzorzec URL                         | Chmura | Składnik/funkcji         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |Usługa uwierzytelniania usługi AAD      |
| `https://login.microsoftonline.us` | Rząd USA |Usługa uwierzytelniania usługi AAD      |

Aby uzyskać pełną listę adresów URL wzorce dla urządzeń z serii StorSimple 8000, przejdź do [wzorców adresów URL dla reguł zapory](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Adres URL uwierzytelniania nie znajduje się w regułach zapory poza Data zaprzestania, jeśli urządzenie działa aktualizacji 5, użytkownicy widzą alert adresu URL. Użytkownicy muszą zawierać nowy adres URL uwierzytelniania. Jeśli urządzenie działa wersja przed aktualizacją 5, użytkownicy widzą alert pulsu. W każdym przypadku urządzenia StorSimple nie może uwierzytelnić w usłudze, a usługa nie jest w stanie nawiązać połączenia z urządzeniem.

## <a name="device-version-and-authentication-changes"></a>Zmiany wersji i uwierzytelnianie urządzenia

Jeśli z urządzenia z serii StorSimple 8000, skorzystaj z poniższej tabeli, aby ustalić, jakie działania należy podjąć oparta na wersji oprogramowania urządzenia, którym jest uruchomiony.

| Jeśli urządzenie korzysta| Wykonaj następującą akcję                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| Aktualizacji 5 lub nowszy, a urządzenie jest w trybie offline. <br> Zostanie wyświetlony alert, że adres URL nie jest białej.| Modyfikowanie reguły zapory w celu uwzględnienia adres URL uwierzytelniania.<br> Zobacz [adresów URL uwierzytelniania](#url-changes-for-aad-authentication). |
| Aktualizacji 5 lub nowszy i urządzeniem w trybie online.| Nie jest wymagana żadna akcja.                                       |
| Update 4 lub starszym, a urządzenie jest w trybie offline. | Modyfikowanie reguły zapory w celu uwzględnienia adres URL uwierzytelniania.<br>[Pobieranie aktualizacji 5 za pośrednictwem serwera katalogu](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>[Zastosowanie aktualizacji 5 za pomocą metody poprawkę](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix). <br> [Pobierz klucz rejestracji usługi AAD z usługi](#aad-based-registration-keys). <br> [Nawiązania połączenia interfejsu programu Windows PowerShell urządzenia serii StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>Użyj `Redo-DeviceRegistration` polecenia cmdlet, aby zarejestrować urządzenia za pomocą programu Windows PowerShell. Określ klucz uzyskanego w poprzednim kroku.|
| Update 4 lub starszym, a urządzenie jest w trybie online. |Modyfikowanie reguły zapory w celu uwzględnienia adres URL uwierzytelniania.<br> Instalowanie aktualizacji 5 za pomocą portalu Azure.              |
| Resetowanie do wersji przed aktualizacją 5 do ustawień fabrycznych.      |Portal zawiera klucz rejestracji opartych na usłudze AAD urządzeniu jest uruchomiona starsza oprogramowania. Wykonaj kroki opisane w poprzednim scenariuszu w przypadku gdy urządzenie jest uruchomiona aktualizacji 4 lub starszym.              |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji opartych na usłudze AAD

Począwszy od aktualizacji 5 dla urządzeń z serii StorSimple 8000, nowej rejestracji na podstawie AAD klucze są używane. Klucze rejestracji jest używany do rejestrowania usługi Menedżer StorSimple urządzenia z urządzeniem.

Nie można używać nowych kluczy rejestracji usługi AAD, jeśli używasz urządzeniu z serii StorSimple 8000 systemem aktualizacji 4 lub starszym (w tym starszych urządzeń aktywowane teraz).
W tym scenariuszu należy ponownie wygenerować klucz rejestracji usługi. Po wygenerowaniu klucza nowy klucz służy do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji usługi AAD wygasa po 3 dni.
- Klucze rejestracji usługi AAD pracować tylko z urządzeń z serii StorSimple 8000 z aktualizacją 5 lub nowszym.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiadające im klucze rejestracji ACS.

Wykonaj poniższe kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **Menedżera urządzeń StorSimple**, przejdź do **zarządzania &gt;**  **klucze**. Umożliwia także pasek wyszukiwania do wyszukiwania _klucze_.
    
2. Kliknij przycisk **generowanie klucza**.

    ![Kliknij przycisk regenerate](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Klucz starsze przestanie działać.

    ![Potwierdź regenerate](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > W przypadku tworzenia urządzenia chmury StorSimple w zarejestrowany do Twojego urządzenia z serii StorSimple 8000 z aktualizacją usługi, nie generują klucz rejestracji podczas tworzenia jest w toku. Poczekaj na zakończenie, a następnie wygeneruj klucz rejestracji tworzenia.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o sposobie wdrażania [urządzenia z serii StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).

