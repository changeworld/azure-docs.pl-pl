---
title: Co to jest zarządzanie certyfikatami w usłudze Azure IoT OPC UA | Dokumentacja firmy Microsoft
description: Omówienie magazynu OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759288"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>Co to jest zarządzanie certyfikatami w usłudze Azure IoT Otwórz platformy łączności (OPC) UA?

Usługa Azure IoT OPC UA zarządzanie certyfikatami, również wiedzieć, co magazyn OPC są mikrousługi można skonfigurować, zarejestruj się i zarządzanie cyklem życia certyfikatu w przypadku aplikacji serwera i klienta OPC UA w chmurze. W tym artykule opisano magazynu OPC prostych zastosowań.

## <a name="certificate-management"></a>Zarządzanie certyfikatami

Na przykład firmy produkcyjnej związanych musi połączyć ich maszynę serwera OPC UA do swojej aplikacji w nowo utworzonego klienta. Gdy producent sprawia, że początkowe dostęp do komputera serwera, w aplikacji serwera OPC UA, aby wskazać, że aplikacja kliencka nie jest bezpieczny natychmiast wyświetlany jest komunikat o błędzie. Ten mechanizm jest wbudowana w komputerze serwera OPC UA, aby uniemożliwić dostęp dowolnej nieautoryzowanych aplikacji, co uniemożliwia vicious stosowanie metod hakerskich produkcyjnych.

## <a name="application-security-management"></a>Zarządzanie zabezpieczeniami aplikacji
Professional zabezpieczeń używa magazynu OPC mikrousługi można łatwo włączyć serwer OPC UA do komunikowania się z dowolnej aplikacji klienckiej, ponieważ Magazyn OPC ma wszystkie funkcje rejestru certyfikatu, magazynu i zarządzanie cyklem życia. Teraz bezpiecznie jest połączony z serwerem OPC UA, będzie mogła komunikować się do aplikacji nowo utworzonego klienta

## <a name="the-complete-opc-vault-architecture"></a>Pełnej architektury magazynu OPC
Na poniższym diagramie przedstawiono pełnej architektury OPC magazynu.

![Architektura magazynu OPC](media/overview-opc-vault-architecture/opc-vault.png)