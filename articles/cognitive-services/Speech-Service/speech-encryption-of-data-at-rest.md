---
title: Szyfrowanie danych w stanie spoczynku w usłudze mowy
titleSuffix: Azure Cognitive Services
description: Szyfrowanie usług mowy danych w spoczynku.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372360"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Szyfrowanie danych w stanie spoczynku w usłudze mowy

Usługa mowy automatycznie szyfruje dane, gdy są one utrwalone w chmurze. Szyfrowanie usługi mowy chroni dane i pomaga w spełnie przestrzeganie zobowiązań dotyczących zabezpieczeń i zgodności organizacji.

## <a name="about-cognitive-services-encryption"></a>Szyfrowanie usług Cognitive Services — informacje

Dane są szyfrowane i odszyfrowywane przy użyciu [256-bitowego](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) szyfrowania AES zgodnego ze standardem [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane dla Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu lub aplikacji, aby skorzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Zarządzanie kluczami szyfrowania — informacje

Podczas korzystania z mowy niestandardowej i niestandardowego głosu usługa Mowy może przechowywać następujące dane w chmurze:  

* Dane śledzenia mowy - tylko wtedy, gdy włączysz śledzenie niestandardowego punktu końcowego
* Przesłane dane szkoleniowe i testowe

Domyślnie dane są przechowywane w magazynie firmy Microsoft, a subskrypcja korzysta z kluczy szyfrowania zarządzanych przez firmę Microsoft. Masz również możliwość przygotowania własnego konta magazynu. Dostęp do magazynu jest zarządzany przez tożsamość zarządzaną, a usługa Speech nie może bezpośrednio uzyskać dostępu do własnych danych, takich jak dane śledzenia mowy, dane szkoleniowe dostosowywania i modele niestandardowe.

Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [Jakie są tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Przynieś własną pamięć masową (BYOS) do dostosowywania i rejestrowania

Aby poprosić o dostęp do własnego magazynu, wypełnij i prześlij [usługę mowy — przynieś własny formularz żądania magazynu (BYOS).](https://aka.ms/cogsvc-cmk) Po zatwierdzeniu musisz utworzyć własne konto magazynu, aby przechowywać dane wymagane do dostosowania i rejestrowania. Podczas dodawania konta magazynu zasób usługi mowy włączy system przypisany tożsamości zarządzanej. Po włączeniu tożsamości zarządzanej przypisanej przez system ten zasób zostanie zarejestrowany w usłudze Azure Active Directory (AAD). Po zarejestrowaniu, tożsamość zarządzana otrzyma dostęp do konta magazynu. Więcej informacji na temat tożsamości zarządzanych można znaleźć tutaj. Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [Jakie są tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Jeśli system został wyłączony przypisane tożsamości zarządzane, dostęp do konta magazynu zostanie usunięty. Spowoduje to, że części usługi Speech, które wymagają dostępu do konta magazynu, przestaną działać.  

## <a name="regional-availability"></a>Dostępność regionalna

BYOS jest obecnie dostępny w następujących regionach:

* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA

## <a name="next-steps"></a>Następne kroki

* [Usługa mowy — przynieś własny formularz żądania magazynu (BYOS)](https://aka.ms/cogsvc-cmk)
* [Co to są tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
