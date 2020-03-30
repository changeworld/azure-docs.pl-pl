---
title: Co to jest OPC Vault - Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie programu OPC Vault. Może konfigurować, rejestrować i zarządzać cyklem życia certyfikatów dla aplikacji OPC UA w chmurze.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826214"
---
# <a name="what-is-opc-vault"></a>Co to jest OPC Vault?

OPC Vault to mikrousługa, która może konfigurować, rejestrować i zarządzać cyklem życia certyfikatów dla serwerów OPC UA i aplikacji klienckich w chmurze. W tym artykule opisano proste przypadki użycia programu OPC Vault.

## <a name="certificate-management"></a>Zarządzanie certyfikatami

Na przykład firma produkcyjna musi podłączyć komputer serwera OPC UA do nowo utworzonej aplikacji klienckiej. Gdy producent uzyskuje początkowy dostęp do komputera serwera, komunikat o błędzie jest natychmiast wyświetlany w aplikacji serwera OPC UA, aby wskazać, że aplikacja kliencka nie jest bezpieczna. Mechanizm ten jest wbudowany w maszynę serwera OPC UA, aby zapobiec nieautoryzowanemu dostępowi do aplikacji, co zapobiega błędnemu włamaniu na hali produkcyjnej.

## <a name="application-security-management"></a>Zarządzanie zabezpieczeniami aplikacji
Specjalista w dziedzinie zabezpieczeń używa mikrousługI OPC Vault, aby łatwo włączyć serwer OPC UA do komunikowania się z dowolną aplikacją kliencką, ponieważ OPC Vault ma wszystkie funkcje do zarządzania rejestrem certyfikatów, magazynem i cyklem życia. Teraz serwer OPC UA jest bezpiecznie podłączony, może komunikować się z nowo wybudowaną aplikacją kliencką

## <a name="the-complete-opc-vault-architecture"></a>Pełna architektura OPC Vault
Na poniższym diagramie przedstawiono pełną architekturę OPC Vault.

![Architektura OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dowiedziałeś się o OPC Vault i jego zastosowaniach, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Architektura OPC Vault](overview-opc-vault-architecture.md)
