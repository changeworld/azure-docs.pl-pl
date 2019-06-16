---
title: Kontrolowana Walidacja hybrydowych usługi Azure AD join — usługa Azure AD
description: Dowiedz się, jak to zrobić kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD przed jego włączeniem w całej organizacji do wszystkich naraz
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: article
ms.date: 05/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b388f92a875fb2635037a6eae3ff3b6a95793
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66513289"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Kontrolowana walidacja przyłączenia do hybrydowej usługi Azure AD

Gdy wszystkie wymagania wstępne zostały spełnione, urządzenia Windows są automatycznie rejestrowane jako urządzenia w Twojej dzierżawie usługi Azure AD. Stan tych tożsamości urządzeń w usłudze Azure AD jest określane jako dołączenie do hybrydowej usługi Azure AD. Więcej informacji na temat pojęć omówione w tym artykule znajdują się w artykułach [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md) i [planowanie implementacji hybrydowej usługi Azure Active Directory join ](hybrid-azuread-join-plan.md).

Organizacje mogą chcieć wykonać kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD przed jego włączeniem w całej organizacji wszystkie na raz. W tym artykule wyjaśniono, jak wykonać kontrolowana Walidacja dołączenie do hybrydowej usługi Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Kontrolowana Walidacja hybrydowych usługi Azure AD join w Windows bieżące urządzenia

Dla urządzeń z systemem Windows pulpitu systemu operacyjnego, obsługiwana wersja to Windows Update rozliczenia 10 (wersja 1607) lub nowszej. Najlepszym rozwiązaniem jest uaktualnienie do najnowszej wersji systemu Windows 10.

Kontrolowana Walidacja hybrydowych usługi Azure AD join w bieżące urządzenia Windows celu należy:

1. Wyczyść wpis punktu połączenia usługi (SCP) z usługi Active Directory (AD), jeśli istnieje
1. Skonfiguruj ustawienia rejestru po stronie klienta dla punktu połączenia usługi na komputerach przyłączonych do domeny za pomocą obiektu zasad grupy (GPO)
1. Jeśli używasz usług AD FS należy również skonfigurować ustawienia rejestru po stronie klienta dla punktu połączenia usługi na serwerze usług AD FS przy użyciu obiektu zasad grupy  



### <a name="clear-the-scp-from-ad"></a>Wyczyść punkt połączenia usługi z usługi AD

Umożliwia modyfikowanie obiektów punkt połączenia usługi w AD usług Edytor interfejsów Active Directory (Edytor ADSI).

1. Uruchom **ADSI Edit** aplikację dwuwarstwową za pomocą i stacji roboczej administratora lub kontrolera domeny jako Administrator przedsiębiorstwa.
1. Połączyć się z **kontekście nazewnictwa konfiguracji** domeny.
1. Przejdź do **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN konfiguracji rejestracji urządzenia**
1. Kliknij prawym przyciskiem myszy obiekt typu liść pod **CN konfiguracji rejestracji urządzenia** i wybierz **właściwości**
   1. Wybierz **słowa kluczowe** z **Edytor atrybutów** oknie i kliknij przycisk **edycji**
   1. Wybierz wartości **azureADId** i **azureADName** (po jednym naraz) i kliknij przycisk **Usuń**
1. Zamknij **ADSI: Edycja**


### <a name="configure-client-side-registry-setting-for-scp"></a>Skonfiguruj ustawienia rejestru po stronie klienta dla punktu połączenia usługi

Skorzystaj z następującego przykładu, aby utworzyć obiekt zasad grupy (GPO) do Wdróż ustawienie rejestru, konfigurowanie wpis punktu połączenia usługi w rejestrze urządzeń.

1. Otwórz konsolę zarządzania zasadami grupy i utworzyć nowy obiekt zasad grupy w domenie.
   1. Podaj swoje nowo utworzony obiekt zasad grupy nazwę (na przykład ClientSideSCP).
1. Edytowanie obiektu zasad grupy i Znajdź następującą ścieżkę: **Konfiguracja komputera** > **preferencje** > **ustawienia Windows** > **rejestru**
1. Kliknij prawym przyciskiem myszy, rejestru i wybierz pozycję **New** > **element rejestru**
   1. Na **ogólne** skonfiguruj następujące czynności
      1. Akcja: **Aktualizacja**
      1. Gałąź: **HKEY_LOCAL_MACHINE**
      1. Ścieżka klucza: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nazwa wartości: **Identyfikator dzierżawy**
      1. Typ wartości: **REG_SZ**
      1. Dane wartości: Identyfikator GUID lub **identyfikator katalogu** wystąpienia usługi Azure AD (tę wartość można znaleźć w **witryny Azure portal** > **usługi Azure Active Directory**  >   **Właściwości** > **identyfikator katalogu**)
   1. Kliknij przycisk **OK**.
1. Kliknij prawym przyciskiem myszy, rejestru i wybierz pozycję **New** > **element rejestru**
   1. Na **ogólne** skonfiguruj następujące czynności
      1. Akcja: **Aktualizacja**
      1. Gałąź: **HKEY_LOCAL_MACHINE**
      1. Ścieżka klucza: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nazwa wartości: **tenantName**
      1. Typ wartości: **REG_SZ**
      1. Dane wartości: Twoje zweryfikowanych **nazwy domeny** w usłudze Azure AD (na przykład `contoso.onmicrosoft.com` lub inna nazwa zweryfikowanej domeny w katalogu)
   1. Kliknij przycisk **OK**.
1. Zamknij Edytor dla nowo utworzonego obiektu zasad grupy
1. Połącz nowo utworzony obiekt zasad grupy do żądanej jednostki Organizacyjnej zawierającej komputery z przyłączonym do domeny, które należą do Twojej populacji kontrolowane wprowadzanie

### <a name="configure-ad-fs-settings"></a>Konfigurowanie ustawień usług AD FS

Jeśli używasz usług AD FS, należy najpierw skonfigurować punkt połączenia usługi po stronie klienta przy użyciu instrukcji wymienionych powyżej, ale łączenia obiektu zasad grupy na serwerach usług AD FS. Ta konfiguracja jest wymagany dla usług AD FS do ustalenia źródła tożsamości urządzeń jako usługi Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Kontrolowana Walidacja hybrydowych usługi Azure AD join w Windows niższego poziomu urządzeń

Aby zarejestrować urządzenia niskiego poziomu Windows, należy zainstalować organizacje [Microsoft dołączania komputerów do systemu Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) dostępne w Microsoft Download Center.

Pakiet można wdrożyć za pomocą to system dystrybucji oprogramowania, takie jak [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Pakiet obsługuje opcje standardowej instalacji dyskretnej, za pomocą parametru cichy. Current branch programu Configuration Manager zapewnia korzyści w porównaniu ze starszymi wersjami, takich jak możliwość śledzenia rejestracji zakończonych.

Instalator jest utworzenie zaplanowanego zadania w systemie, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do Windows. Zadanie dyskretnie łączy urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika, po uwierzytelnieniu w usłudze Azure AD.

Aby kontrolować rejestracji urządzeń, należy wdrożyć pakiet Instalatora Windows do wybranej grupy systemu Windows niższego poziomu urządzeń.

> [!NOTE]
> Jeśli punkt połączenia usługi nie jest skonfigurowana w AD, a następnie należy wykonać to samo podejście, zgodnie z opisem na [skonfigurować ustawienie rejestru po stronie klienta dla punktu połączenia usługi](#configure-client-side-registry-setting-for-scp)) na komputerach przyłączonych do domeny za pomocą obiektu zasad grupy (GPO).


Po upewnieniu się, że wszystko działa zgodnie z oczekiwaniami, można automatycznie zarejestrować pozostałe urządzenia bieżącej i wcześniejszych Windows z usługą Azure AD przez [konfigurowania punktu połączenia usługi przy użyciu usługi Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Kolejne kroki

[Planowanie implementacji z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)
