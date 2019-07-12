---
title: Co to jest magazyn OPC — Azure | Dokumentacja firmy Microsoft
description: Omówienie magazynu OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 40a9016ac7a10175b51f0fb6f072dd089bde3a51
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606275"
---
# <a name="what-is-opc-vault"></a>Co to jest magazyn OPC?

OPC Vault to mikrousług, konfigurowanie, rejestrowanie i zarządzanie cyklem życia certyfikatu w przypadku serwera OPC UA i aplikacje klienckie w chmurze. W tym artykule opisano magazynu OPC prostych zastosowań.

## <a name="certificate-management"></a>Zarządzanie certyfikatami

Na przykład firmy produkcyjnej związanych musi połączyć ich maszynę serwera OPC UA do swojej aplikacji w nowo utworzonego klienta. Gdy producent sprawia, że początkowe dostęp do komputera serwera, w aplikacji serwera OPC UA, aby wskazać, że aplikacja kliencka nie jest bezpieczny natychmiast wyświetlany jest komunikat o błędzie. Ten mechanizm jest wbudowana w komputerze serwera OPC UA, aby uniemożliwić dostęp dowolnej nieautoryzowanych aplikacji, co uniemożliwia vicious stosowanie metod hakerskich produkcyjnych.

## <a name="application-security-management"></a>Zarządzanie zabezpieczeniami aplikacji
Professional zabezpieczeń używa magazynu OPC mikrousługi można łatwo włączyć serwer OPC UA do komunikowania się z dowolnej aplikacji klienckiej, ponieważ Magazyn OPC ma wszystkie funkcje rejestru certyfikatu, magazynu i zarządzanie cyklem życia. Teraz bezpiecznie jest połączony z serwerem OPC UA, będzie mogła komunikować się do aplikacji nowo utworzonego klienta

## <a name="the-complete-opc-vault-architecture"></a>Pełnej architektury magazynu OPC
Na poniższym diagramie przedstawiono pełnej architektury OPC magazynu.

![Architektura magazynu OPC](media/overview-opc-vault-architecture/opc-vault.png)