---
title: Jak zarządzać nieaktywnymi urządzeniami w usłudze Azure AD | Microsoft Docs
description: Learn how to remove stale devices from your database of registered devices in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1829c56f9804c5aa808461db98a5048d63f55446
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207278"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>How To: Manage stale devices in Azure AD

W idealnym przypadku, aby zakończyć cykl życia, zarejestrowane urządzenia powinny zostać wyrejestrowane, gdy nie są już potrzebne. Ale czasami, na przykład z powodu zgubienia, kradzieży lub uszkodzenia urządzenia albo ponownej instalacji systemu operacyjnego, w Twoim środowisku pojawiają się nieaktywne urządzenia. Jako administrator IT prawdopodobnie potrzebujesz metody usuwania takich nieaktywnych urządzeń, aby Twoje zasoby mogły skoncentrować się na zarządzaniu urządzeniami, które faktycznie wymagają zarządzania.

W tym artykule dowiesz się, jak skutecznie zarządzać nieaktywnymi urządzeniami we własnym środowisku.
  

## <a name="what-is-a-stale-device"></a>Co to jest nieaktywne urządzenie?

Nieaktywnym nazywamy urządzenie, które zostało zarejestrowane za pomocą usługi Azure AD, ale nie było używane do uzyskiwania dostępu do aplikacji w chmurze w określonym przedziale czasu. Nieaktywne urządzenia wpływają na możliwość obsługi urządzeń i użytkowników oraz zarządzania nimi w ramach dzierżawy z następujących powodów: 

- Zduplikowane urządzenia mogą utrudnić pracownikom działu pomocy technicznej zidentyfikowanie urządzenia, które jest aktualnie aktywne.
- An increased number of devices creates unnecessary device writebacks increasing the time for Azure AD connect syncs.
- W celu zachowania porządku i spełnienia wymagań dotyczących zgodności warto mieć „czysty” stan urządzeń. 

Istnienie nieaktywnych urządzeń w usłudze Azure AD może być niezgodne z ogólnymi zasadami cyklu życia urządzeń w Twojej organizacji.

## <a name="detect-stale-devices"></a>Wykrywanie nieaktywnych urządzeń

Ponieważ nieaktywne urządzenie jest zdefiniowane jako zarejestrowane urządzenie, które nie było używane do uzyskiwania dostępu do aplikacji w chmurze w określonym przedziale czasu, wykrywanie nieaktywnych urządzeń wymaga właściwości powiązanej ze znacznikiem czasu. W usłudze Azure AD ta właściwość nazywa się **ApproximateLastLogonTimestamp** lub **znacznik czasu aktywności**. Jeśli różnica między bieżącą datą a wartością **znacznika czasu aktywności** przekroczy przedział czasu zdefiniowany dla aktywnych urządzeń, urządzenie jest uznawane za nieaktywne. Ten **znacznik czasu aktywności** znajduje się obecnie w publicznej wersji zapoznawczej.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Jak jest zarządzana wartość znacznika czasu aktywności?  

Obliczanie znacznika czasu aktywności jest wyzwalane przez próbę uwierzytelnienia urządzenia. Usługa Azure AD oblicza znacznik czasu aktywności w następujących sytuacjach:

- A Conditional Access policies requiring [managed devices](../conditional-access/require-managed-devices.md) or [approved client apps](../conditional-access/app-based-conditional-access.md) has been triggered.
- Wykazywanie aktywności w sieci przez urządzenia z systemem Windows 10, które są dołączone do usługi Azure AD lub dołączone hybrydowo do usługi Azure AD. 
- Zaewidencjonowanie w usłudze urządzeń zarządzanych przy użyciu usługi Intune.

If the delta between the existing value of the activity timestamp and the current value is more than 14 days (+/-5 day variance), the existing value is replaced with the new value.

## <a name="how-do-i-get-the-activity-timestamp"></a>Jak mogę uzyskać znacznik czasu aktywności?

Istnieją dwie możliwości uzyskania wartości znacznika czasu aktywności:

- Kolumna **Aktywność** na [stronie urządzeń](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) w witrynie Azure Portal

    ![Znacznik czasu aktywności](./media/manage-stale-devices/01.png)

- Polecenie cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Znacznik czasu aktywności](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Planowanie oczyszczania nieaktywnych urządzeń

Aby skutecznie oczyścić nieaktywne urządzenia w swoim środowisku, zdefiniuj powiązane zasady. Te zasady pomagają zapewnić uwzględnienie wszystkich zagadnień dotyczących nieaktywnych urządzeń. Poniższe sekcje zawierają przykłady typowych zagadnień uwzględnianych w zasadach. 

### <a name="cleanup-account"></a>Konto oczyszczania

Aby zaktualizować urządzenie w usłudze Azure AD, potrzebne jest konto, które ma przypisaną jedną z następujących ról:

- Administrator globalny
- Cloud Device Administrator
- Administrator usługi Intune

W zasadach oczyszczania wybierz konta, które mają przypisane wymagane role. 

### <a name="timeframe"></a>Ramy czasowe

Zdefiniuj przedział czasu, który jest wskaźnikiem służącym do wykrywania nieaktywnego urządzenia. When defining your timeframe, factor the window noted for updating the activity timestamp into your value. For example, you shouldn't consider a timestamp that is younger than 21 days (includes variance) as an indicator for a stale device. Istnieją sytuacje, w których urządzenie może wyglądać na nieaktywne, chociaż tak nie jest. Na przykład gdy właściciel urządzenia jest na urlopie lub na zwolnieniu lekarskim  przekraczającym przedział czasu zdefiniowany dla nieaktywnych urządzeń.

### <a name="disable-devices"></a>Wyłączanie urządzeń

Nie zaleca się natychmiastowego usuwania urządzenia, które wydaje się być nieaktualne, ponieważ takiego usunięcia nie można cofnąć, gdy uznanie urządzenia za nieaktywne okaże się wynikiem fałszywie dodatnim. Najlepszym rozwiązaniem jest wyłączenie urządzenia na okres prolongaty przed jego usunięciem. W zasadach zdefiniuj przedział czasu wyłączenia urządzenia przed jego usunięciem.

### <a name="mdm-controlled-devices"></a>Urządzenia kontrolowane przez rozwiązanie MDM

Jeśli urządzenie jest kontrolowane przez usługę Intune lub dowolne inne rozwiązanie do zarządzania urządzeniami mobilnymi (MDM), wycofaj to urządzenie z takiego rozwiązania przed jego wyłączeniem lub usunięciem.

### <a name="system-managed-devices"></a>Urządzenia zarządzane przez system

Nie należy usuwać urządzeń zarządzanych przez system. Są to zazwyczaj urządzenia takie jak autopilot. Once deleted, these devices can't be reprovisioned. Nowe polecenie cmdlet `get-msoldevice` domyślnie wyklucza urządzenia zarządzane przez system. 

### <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

W przypadku urządzeń dołączonych hybrydowo do usługi Azure AD powinny być przestrzegane lokalne zasady zarządzania nieaktywnymi urządzeniami. 

Oczyszczanie w usłudze Azure AD:

- **Urządzenia z systemem Windows 10** — wyłącz lub usuń urządzenia z systemem Windows 10 w lokalnej usłudze AD i pozwól usłudze Azure AD Connect zsynchronizować zmieniony stan urządzenia z usługą Azure AD.
- **Windows 7/8** - Disable or delete Windows 7/8 devices in your on-premises AD first. Nie możesz używać programu Azure AD Connect do wyłączania ani usuwania urządzeń z systemem Windows 7 lub 8 w usłudze Azure AD. Instead, when you make the change in your on-premises, you must disable/delete in Azure AD.

> [!NOTE]
>* Deleting devices in your on-premises AD or Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access). Read additional information on how to [remove registration on the client](faq.md#hybrid-azure-ad-join-faq).
>* Deleting a Windows 10 device only in Azure AD will re-synchronize the device from your on-premises using Azure AD connect but as a new object in "Pending" state. A re-registration is required on the device.
>* Removing the device from sync scope for Windows 10/Server 2016 devices will delete the Azure AD device. Adding it back to sync scope will place a new object in "Pending" state. A re-registration of the device is required.
>* If you not using Azure AD Connect for Windows 10 devices to synchronize (e.g. ONLY using AD FS for registration), you must manage lifecycle similar to Windows 7/8 devices.


### <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Urządzenia dołączone do usługi Azure AD wyłącza się lub usuwa w usłudze Azure AD.

> [!NOTE]
>* Deleting an Azure AD device does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g Conditional Access). 
>* Read more on [how to unjoin on Azure AD](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD

Urządzenia zarejestrowane w usłudze Azure AD wyłącza się lub usuwa w usłudze Azure AD.

> [!NOTE]
>* Deleting an Azure AD registered device in Azure AD does not remove registration on the client. It will only prevent access to resources using device as an identity (e.g. Conditional Access).
>* Read more on [how to remove a registration on the client](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Oczyszczanie nieaktywnych urządzeń w witrynie Azure Portal  

Nieaktywne urządzenia można oczyszczać w witrynie Azure Portal, ale bardziej wydajne jest wykonywanie tego za pomocą skryptu programu PowerShell. Użyj najnowszego modułu programu PowerShell w wersji 1, aby za pomocą filtru znacznika czasu odfiltrować urządzenia zarządzane przez system, takie jak autopilot. W tym przypadku użycie programu PowerShell w wersji 2 nie jest zalecane.

Typowa procedura obejmuje następujące czynności:

1. Nawiązywanie połączenia z usługą Azure Active Directory przy użyciu polecenia cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)
1. Pobieranie listy urządzeń
1. Wyłączanie urządzenia przy użyciu polecenia cmdlet [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0) 
1. Przed usunięciem urządzenia poczekaj, aż upłynie wybrana przez Ciebie liczba dni okresu prolongaty.
1. Usuwanie urządzenia przy użyciu polecenia cmdlet [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0)

### <a name="get-the-list-of-devices"></a>Pobieranie listy urządzeń

Aby uzyskać listę wszystkich urządzeń i zachować zwrócone dane w pliku CSV, użyj następującego polecenia:

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

If you have a large number of devices in your directory, use the timestamp filter to narrow down the number of returned devices. Aby uzyskać wszystkie urządzenia ze znacznikiem czasu starszym niż określona data oraz zachować zwrócone dane w pliku CSV, użyj następującego polecenia: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Dlaczego znacznik czasu nie jest aktualizowany częściej?

Znacznik czasu jest aktualizowany w celu obsługi scenariuszy cyklu życia urządzenia. Nie jest to inspekcja. Aby wykonywać częstsze aktualizacje na urządzeniu, użyj dzienników inspekcji logowania.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Dlaczego muszę zadbać o klucze funkcji BitLocker?

Skonfigurowane klucze funkcji BitLocker dla urządzeń z systemem Windows 10 są przechowywane w obiekcie urządzenia w usłudze Azure AD. Jeśli usuwasz nieaktywne urządzenie, usuwasz również klucze funkcji BitLocker, które są przechowywane na tym urządzeniu. Przed usunięciem nieaktywnego urządzenia musisz sprawdzić, czy zasady oczyszczania są zgodne z rzeczywistym cyklem życia urządzenia. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Why should I worry about Windows Autopilot devices?

When a Azure AD device was associated with a Windows Autopilot object the following three scenarios can occur if the device will be repurposed in future:
- With Windows Autopilot user-driven deployments without using white glove, a new Azure AD device will be created, but it won’t be tagged with the ZTDID.
- With Windows Autopilot self-deploying mode deployments, they will fail because an associate Azure AD device cannot be found.  (This is a security mechanism to make sure that no “imposter” devices try to join Azure AD with no credentials.) The failure will indicate a ZTDID mismatch.
- With Windows Autopilot white glove deployments, they will fail because an associated Azure AD device cannot be found. (Behind the scenes, white glove deployments use the same self-deploying mode process, so they enforce the same security mechanisms.)

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Jak mogę sprawdzić, czy wszystkie typy urządzeń zostały dołączone?

Aby dowiedzieć się więcej na temat różnych typów, zobacz [omówienie zarządzania urządzeniami](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Co się stanie, gdy urządzenie zostanie wyłączone?

Każde uwierzytelnianie z użyciem urządzenia w usłudze Azure AD jest odrzucane. Typowe przykłady:

- **Urządzenie dołączone hybrydowo do usługi Azure AD** — użytkownicy mogą korzystać z urządzenia, aby zalogować się do swojej domeny lokalnej. Nie mogą jednak uzyskać dostępu do zasobów usługi Azure AD, takich jak usługa Office 365.
- **Urządzenia dołączone do usługi Azure AD** — użytkownicy nie mogą używać urządzenia do logowania. 
- **Urządzenia przenośne** — użytkownicy nie mogą uzyskać dostępu do zasobów usługi Azure AD, takich jak usługa Office 365. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)
