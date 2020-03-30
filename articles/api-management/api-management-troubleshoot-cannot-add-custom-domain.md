---
title: Nie można dodać domeny niestandardowej przy użyciu certyfikatu Usługi Key Vault
titleSuffix: Azure API Management
description: Dowiedz się, jak rozwiązać problem, w którym nie można dodać domeny niestandardowej w usłudze Azure API Management przy użyciu certyfikatu magazynu kluczy.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430575"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Nie można zaktualizować nazwy hostów usługi api Management

W tym artykule opisano błąd "Nie można zaktualizować nazwy hostów usługi zarządzania interfejsami API", który może wystąpić podczas dodawania domeny niestandardowej dla usługi Azure API Management. Ten artykuł zawiera kroki rozwiązywania problemów ułatwiające rozwiązanie problemu.

## <a name="symptoms"></a>Objawy

Podczas próby dodania domeny niestandardowej dla usługi zarządzania interfejsami API przy użyciu certyfikatu z usługi Azure Key Vault zostanie wyświetlony następujący komunikat o błędzie:

- Nie można zaktualizować nazwy hostów usługi api management. Żądanie dohttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0zasobu ' ' nie powiodło się z StatusCode: Zabronione dla RequestId: . Komunikat o wyjątku: Operacja zwróciła nieprawidłowy kod stanu "Zabronione".

## <a name="cause"></a>Przyczyna

Usługa zarządzania interfejsami API nie ma uprawnień dostępu do magazynu kluczy, którego próbujesz użyć dla domeny niestandardowej.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Przejdź do [witryny Azure portal](Https://portal.azure.com), wybierz wystąpienie usługi API Management, a następnie wybierz pozycję Zarządzane **tożsamości**. Upewnij się, że opcja **Zarejestruj się za pomocą usługi Azure Active Directory** jest ustawiona na **Tak**. 
    ![Rejestrowanie się za pomocą usługi Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. W witrynie Azure Portal otwórz usługę **Magazyny kluczy** i wybierz magazyn kluczy, którego próbujesz użyć dla domeny niestandardowej.
1. Wybierz **pozycję Zasady programu Access**i sprawdź, czy istnieje podmiot usługi, który pasuje do nazwy wystąpienia usługi zarządzanie interfejsami API. Jeśli tak, wybierz jednostkę usługi i upewnij się, że ma uprawnienia **Pobierz** wymienione w obszarze **Uprawnienia tajne**.  
    ![Dodawanie zasad dostępu dla jednostki usługi](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Jeśli usługi api Management nie ma na liście, wybierz pozycję **Dodaj zasady dostępu**, a następnie utwórz następujące zasady dostępu:
    - **Konfigurowanie z szablonu**: Brak
    - **Wybierz głównego:** Wyszukaj nazwę usługi API Management, a następnie wybierz ją z listy
    - **Uprawnienia klucza:** Brak
    - **Tajne uprawnienia:** Pobierz
    - **Uprawnienia certyfikatów**: Brak
1. Wybierz **przycisk OK,** aby utworzyć zasady dostępu.
1. Wybierz **pozycję Zapisz,** aby zapisać zmiany.

Sprawdź, czy problem został rozwiązany. Aby to zrobić, spróbuj utworzyć domenę niestandardową w usłudze zarządzania interfejsami API przy użyciu certyfikatu Usługi Key Vault.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o usłudze api management:

- Obejrzyj więcej [filmów](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o zarządzaniu interfejsami API.
* Aby uzyskać inne sposoby zabezpieczenia usługi zaplecza, zobacz [Uwierzytelnianie wzajemnego certyfikatu](api-management-howto-mutual-certificates.md).

* [Tworzenie wystąpienia usługi zarządzania interfejsami API](get-started-create-service-instance.md).
* [Zarządzaj pierwszym interfejsem API](import-and-publish.md).