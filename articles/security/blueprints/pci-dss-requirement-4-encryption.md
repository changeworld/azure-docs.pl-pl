---
title: Plan Azure przetwarzania płatności — wymagania dotyczące szyfrowania
description: Wymaganie PCI DSS 4
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6de3290fc2147e3c8ed63642b6e8470093898ef6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Wymagania dotyczące szyfrowania dla środowisk standardem PCI DSS 
## <a name="pci-dss-requirement-4"></a>Wymaganie PCI DSS 4

**Szyfrowanie transmisji danych posiadacza sieciach otwarty, publiczny**

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

Poufne informacje muszą być szyfrowane podczas przesyłania za pośrednictwem sieci, które są łatwo dostępne przez osoby nieupoważnione. Nieprawidłowo skonfigurowane sieci bezprzewodowych i luk w zabezpieczeniach w starszej wersji szyfrowania i protokoły uwierzytelniania nadal cele złośliwych osób, które wykorzystują te luki w zabezpieczeniach na uzyskanie uprzywilejowanego dostępu do posiadacza danych środowisk.

## <a name="pci-dss-requirement-41"></a>Wymaganie PCI DSS 4.1

**4.1** użyć silnej kryptografii i zabezpieczeń protokołów (na przykład protokołu TLS, IPSEC, SSH, itp.) do ochrony danych poufnych posiadacza podczas przesyłania za pośrednictwem sieci otwarty, publiczny, w tym następujące:
- Akceptowane są tylko zaufanych kluczy i certyfikatów.
- Protokół używany obsługuje tylko bezpieczny wersji lub konfiguracji.
- Poziom szyfrowania jest odpowiednia dla metody szyfrowania w użyciu. 

> [!NOTE]
> W przypadku protokołu TLS/SSL wczesne wymagania w dodatku A2 muszą być wypełnione.
>
> Przykłady otwarty, publicznej sieci obejmują, ale nie są ograniczone do:
> - Internet
> - Technologie sieci bezprzewodowej, w tym 802.11 i Bluetooth
> - Komórkowej technologii, na przykład globalnym systemie komunikacji bezprzewodowej (GSM), kod dzielenia wiele form dostępu (CDMA)
> - Usługi ogólne pakietów radiowych (GPRS)
> - Łączności satelitarnej


**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso to rozwiązanie typu PaaS, które implementuje silnej kryptografii dla wdrożenia w następujący sposób:<br /><br />Aby spełnić wymagania zaszyfrowanych danych na rest, [usługi Azure Storage](https://azure.microsoft.com/services/storage/) korzysta z następujących:<br /><br /><ul><li>[Szyfrowanie usługi Magazyn Azure (SSE) dla przechowywanych danych](/azure/storage/storage-service-encryption)</li><li>Baza danych SQL: Wystąpienie bazy danych SQL PaaS służy do pokazują środki bezpieczeństwa bazy danych. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - bazy danych SQL Azure](payment-processing-blueprint.md#azure-sql-database).</li><li>[Szyfrowanie dysków Azure (funkcji Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Za pomocą usługi Azure Key Vault wyrównana z Azure dla instytucji rządowych, wymagania dotyczące HIPAA i PCI DSS.|



### <a name="pci-dss-requirement-411"></a>Wymaganie PCI DSS 4.1.1

**4.1.1** upewnij się, przesyłania danych posiadacza sieci bezprzewodowych lub podłączone do środowiska danych imię, umożliwia wdrożenie silnego szyfrowania do uwierzytelniania i transmisji branży najlepszych rozwiązań (na przykład IEEE 802.11i).

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | W rozwiązaniu nie zostały zaimplementowane sieci bezprzewodowej i SNMP.|



## <a name="pci-dss-requirement-42"></a>Wymaganie PCI DSS 4.2

**4.2** nigdy nie wysyłaj niechronione Miednice przez użytkownika końcowego technologii komunikacji (na przykład wiadomości e-mail, wiadomości błyskawicznych, programu SMS, rozmowy, itp.).

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Magazynu sieci Web firmy Contoso nie ma żadnych rozwiązania do obsługi komunikatów zaimplementowana wysyłających danych numer (PRZESUWANIE) niechronione kontem podstawowym.|



## <a name="pci-dss-requirement-43"></a>Wymaganie PCI DSS 4.3

**4.3** upewnij się, że zasady zabezpieczeń i procedur operacyjnych szyfrowanie transmisji danych posiadacza są udokumentowane w użyciu i znane wszystkich zainteresowanych podmiotów.

**Obowiązki:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nie dotyczy. |
| **Odbiorcy<br />(PCI&#8209;DSS&nbsp;plan)** | Klienci są zobowiązani do dokumentowania i szyfrowanie transmisji zawierający dane imię.|




