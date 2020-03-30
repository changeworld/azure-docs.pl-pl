---
title: Kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD — usługa Azure AD
description: Dowiedz się, jak wykonać kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD, zanim włączysz ją w całej organizacji naraz
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049977"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Kontrolowana walidacja przyłączenia do hybrydowej usługi Azure AD

Gdy wszystkie wymagania wstępne są w miejscu, urządzenia z systemem Windows automatycznie zarejestruje się jako urządzenia w dzierżawie usługi Azure AD. Stan tych tożsamości urządzeń w usłudze Azure AD jest określany jako hybrydowe sprzężenie usługi Azure AD. Więcej informacji na temat pojęć omówionych w tym artykule można znaleźć w artykułach [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md) i [planowanie hybrydowej implementacji dołączania usługi Azure Active Directory.](hybrid-azuread-join-plan.md)

Organizacje mogą chcieć wykonać kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD przed włączeniem jej w całej organizacji naraz. W tym artykule opisano, jak wykonać kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD na bieżących urządzeniach z systemem Windows

W przypadku urządzeń z systemem operacyjnym Windows dla komputerów stacjonarnych obsługiwana wersja to Rocznicowa aktualizacja systemu Windows 10 (wersja 1607) lub nowsza. Najlepszym rozwiązaniem jest uaktualnienie do najnowszej wersji systemu Windows 10.

Aby przeprowadzić kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD na bieżących urządzeniach z systemem Windows, należy:

1. Wyczyść wpis Punktu połączenia usługi (SCP) z usługi Active Directory (AD), jeśli istnieje
1. Konfigurowanie ustawienia rejestru po stronie klienta dla protokołu SCP na komputerach przyłączonych do domeny przy użyciu obiektu zasad grupy (GPO)
1. Jeśli używasz usług AD FS, należy również skonfigurować ustawienie rejestru po stronie klienta dla protokołu SCP na serwerze usług AD FS przy użyciu obiektu zasad grupy  
1. Może być również konieczne [dostosowanie opcji synchronizacji](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) w usłudze Azure AD Connect, aby włączyć synchronizację urządzeń. 


### <a name="clear-the-scp-from-ad"></a>Czyszczenie punktu połączenia ZDM z usługi AD

Użyj Edytora interfejsów usług Active Directory (EDYCJA ADSI), aby zmodyfikować obiekty SCP w usłudze AD.

1. Uruchom aplikację klasyczną **ADSI Edit** ze stacji roboczej i administracyjnej lub kontrolera domeny jako administrator przedsiębiorstwa.
1. Połącz się z **kontekstem nazewnictwa konfiguracji** domeny.
1. Przejdź do **CN=Konfiguracja,DC=contoso,DC=com** > **CN=Usługi** > **CN=Konfiguracja rejestracji urządzeń**
1. Kliknij prawym przyciskiem myszy obiekt **liścia CN=62a0ff2e-97b9-4513-943f-0d221bd30080** i wybierz **właściwości**
   1. Wybierz **słowa kluczowe** z okna Edytor **atrybutów** i kliknij pozycję **Edytuj**
   1. Wybierz wartości **azureADId** i **azureADName** (po jednym na raz) i kliknij przycisk **Usuń**
1. Zamknij **edycję ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurowanie ustawienia rejestru po stronie klienta dla protokołu SCP

Poniższy przykład służy do tworzenia obiektu zasad grupy (GPO) w celu wdrożenia ustawienia rejestru konfigurującego wpis punktu dostępu w rejestrze urządzeń.

1. Otwórz konsolę Zarządzanie zasadami grupy i utwórz nowy obiekt zasad grupy w domenie.
   1. Podaj nowo utworzonemu obiektowi zasad grupy nazwę (na przykład ClientSideSCP).
1. Edytowanie obiektu zasad grupy i lokalizowanie następującej ścieżki:**Rejestr** **ustawień ustawień** >  **konfiguracji** > **Preferences** > komputera
1. Kliknij prawym przyciskiem myszy rejestr i wybierz **pozycję Nowy** > **element rejestru**
   1. Na karcie **Ogólne** skonfiguruj następujące
      1. Akcja: **Aktualizacja**
      1. Ul: **HKEY_LOCAL_MACHINE**
      1. Ścieżka **klucza: SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nazwa wartości: **Identyfikator dzierżawy**
      1. Typ wartości: **REG_SZ**
      1. Dane wartości: identyfikator GUID lub **identyfikator katalogu** wystąpienia usługi Azure AD (tę wartość można znaleźć w **identyfikatorze** > katalogu**właściwości usługi** > **Azure Active Directory** > **Azure**azure)
   1. Kliknij **przycisk OK**
1. Kliknij prawym przyciskiem myszy rejestr i wybierz **pozycję Nowy** > **element rejestru**
   1. Na karcie **Ogólne** skonfiguruj następujące
      1. Akcja: **Aktualizacja**
      1. Ul: **HKEY_LOCAL_MACHINE**
      1. Ścieżka **klucza: SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nazwa wartości: **Nazwa najemcy**
      1. Typ wartości: **REG_SZ**
      1. Dane wartości: zweryfikowana **nazwa domeny** w przypadku korzystania ze środowiska federacyjnego, takiego jak AD FS. Zweryfikowana **nazwa domeny** lub onmicrosoft.com nazwę domeny, `contoso.onmicrosoft.com` na przykład, jeśli używasz środowiska zarządzanego
   1. Kliknij **przycisk OK**
1. Zamykanie edytora nowo utworzonego obiektu zasad grupy
1. Łączenie nowo utworzonego obiektu zasad grupy operacyjnej z żądaną łączem organizacyjnym zawierającym komputery przyłączone do domeny, które należą do kontrolowanej populacji wdrażania

### <a name="configure-ad-fs-settings"></a>Konfigurowanie ustawień usług AD FS

Jeśli korzystasz z usług AD FS, należy najpierw skonfigurować protokół SCP po stronie klienta przy użyciu instrukcji wymienionych powyżej, łącząc obiekt zasad grupy z serwerami usług AD FS. Obiekt SCP definiuje źródło uprawnień dla obiektów urządzenia. Może to być lokalnie lub usługi Azure AD. Gdy protokół SCP po stronie klienta jest skonfigurowany dla usług AD FS, źródło obiektów urządzenia jest ustanawiane jako usługa Azure AD.

> [!NOTE]
> Jeśli nie udało się skonfigurować punktu SCP po stronie klienta na serwerach usług AD FS, źródło tożsamości urządzeń będzie traktowane jako lokalne. Następnie program ADFS rozpocznie usuwanie obiektów urządzeń z katalogu lokalnego po upływie określonego okresu zdefiniowanego w atrybucie Rejestracji urządzeń ADFS "MaximumInactiveDays". Obiekty rejestracji urządzeń usługi ADFS można znaleźć za pomocą [polecenia cmdlet Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD na urządzeniach z systemem Windows w dół

Aby zarejestrować urządzenia z systemem Windows w dół, organizacje muszą zainstalować [usługę Microsoft Workplace Join dla komputerów z systemem innych niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) dostępnych w Centrum pobierania Microsoft.

Pakiet można wdrożyć za pomocą systemu dystrybucji oprogramowania, takiego jak [program Microsoft Endpoint Configuration Manager](/configmgr/). Pakiet obsługuje standardowe opcje instalacji cichej z parametrem quiet. Bieżąca gałąź programu Configuration Manager oferuje korzyści w stosunku do wcześniejszych wersji, takich jak możliwość śledzenia zakończonych rejestracji.

Instalator tworzy zaplanowane zadanie w systemie, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD z poświadczeniami użytkownika po uwierzytelnieniu za pomocą usługi Azure AD.

Aby kontrolować rejestrację urządzenia, należy wdrożyć pakiet Instalatora Windows w wybranej grupie urządzeń z systemem Windows w dół.

> [!NOTE]
> Jeśli protokół SCP nie jest skonfigurowany w urzędzie obsługi klienta w urzędzie obsługi klienta, należy postępować zgodnie z tym samym podejściem, co opisane w temacie [Konfigurowanie ustawienia rejestru po stronie klienta dla protokołu SCP)](#configure-client-side-registry-setting-for-scp)na komputerach przyłączonych do domeny przy użyciu obiektu zasad grupy (GPO).


Po sprawdzeniu, czy wszystko działa zgodnie z oczekiwaniami, można automatycznie zarejestrować pozostałe bieżące i urządzenia systemu Windows w usłudze Azure AD, [konfigurując protokół SCP przy użyciu usługi Azure AD Connect.](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)

## <a name="next-steps"></a>Następne kroki

[Planowanie implementacji z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)
