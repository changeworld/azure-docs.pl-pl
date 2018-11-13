---
title: Rozwiązywanie problemów z serwerem konfiguracji podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczące wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/11/2018
ms.author: ramamill
ms.openlocfilehash: b819783d127c51c0d5f33b2273a37a4180cb13a6
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571572"
---
# <a name="troubleshoot-configuration-server-issues"></a>Rozwiązywanie problemów z konfiguracją serwera

Ten artykuł ułatwia rozwiązywanie problemów dotyczących wdrażania i zarządzania nimi [usługi Azure Site Recovery](site-recovery-overview.md) serwera konfiguracji. Serwer konfiguracji jest używana podczas konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych do platformy Azure przy użyciu Site Recovery. 

## <a name="installation-failures"></a>Błędy instalacji

| **komunikat o błędzie** | **Zalecana akcja** |
|--------------------------|------------------------|
|BŁĄD   Nie można załadować kont. Błąd: System.IO.IOException: nie można odczytać danych z połączenia transportowego podczas instalowania i rejestrowania serwera CS.| Upewnij się, że protokół TLS 1.0 jest włączony na komputerze. |

## <a name="registration-failures"></a>Błędy rejestracji

Błędy rejestracji można debugować za pomocą l < br/ogs w folderze %ProgramData%\ASRLogs.

Błędy rejestracji można debugować, przeglądając dzienniki w folderze **%ProgramData%\ASRLogs**.

| **komunikat o błędzie** | **Zalecana akcja** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Komunikat: ACS50008: Token SAML jest nieprawidłowy.<br>Identyfikator śledzenia: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Identyfikator korelacji: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Sygnatura czasowa: **2016-12-12 14:50:08Z<br>** | Upewnij się, że godzina zegara systemowego nie różni się o więcej niż 15 minut od czasu lokalnego. Uruchom ponownie instalatora, aby ukończyć rejestrację.|
|**09:35:27**: wyjątek DRRegistrationException podczas próby pobrania całego magazynu odzyskiwania po awarii dla wybranego certyfikatu: : Zgłoszono Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: Token SAML jest nieprawidłowy.<br>Identyfikator śledzenia: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Identyfikator korelacji: abe9deb8-3e64-464d-8375-36db9816427a<br>Sygnatura czasowa: **2016-05-19 01:35:39Z**<br> | Upewnij się, że godzina zegara systemowego nie różni się o więcej niż 15 minut od czasu lokalnego. Uruchom ponownie instalatora, aby ukończyć rejestrację.|
|06:28:45: Nie można utworzyć certyfikatu<br>06:28:45: Nie można kontynuować instalacji. Nie można utworzyć certyfikatu wymaganego do uwierzytelnienia w usłudze Site Recovery. Uruchom ponownie Instalatora. | Upewnij się, że uruchamiasz Instalatora jako administrator lokalny. |
