---
title: Nie można dodać domeny niestandardowej przy użyciu certyfikatu Key Vault na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak rozwiązać problem polegający na tym, że nie można dodać domeny niestandardowej w usłudze Azure API Management przy użyciu certyfikatu magazynu kluczy.
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
ms.openlocfilehash: 5d31ec21e341c46c2f2d0ab49fdb2d4302c29dc6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121531"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Nie można zaktualizować nazw hostów usługi API Management

W tym artykule opisano błąd "nie można zaktualizować nazw hostów usługi API Management", które mogą wystąpić podczas dodawania domeny niestandardowej dla usługi API Management platformy Azure. W tym artykule opisano kroki rozwiązywania problemów, które ułatwiają rozwiązanie tego problemu.

## <a name="symptoms"></a>Objawy

Gdy próbujesz dodać domenę niestandardową dla usługi API Management przy użyciu certyfikatu z Azure Key Vault, zostanie wyświetlony następujący komunikat o błędzie:

- Nie można zaktualizować nazw hostów usługi API Management. Żądanie do zasobu "https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0" nie powiodło się z StatusCode: Zabronione dla żądania:. Komunikat wyjątku: Operacja zwróciła nieprawidłowy kod stanu "zabroniony".

## <a name="cause"></a>Przyczyna

Usługa API Management nie ma uprawnień dostępu do magazynu kluczy, którego próbujesz użyć dla domeny niestandardowej.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Przejdź do [Azure Portal](Https://portal.azure.com), wybierz wystąpienie API Management, a następnie wybierz pozycję **zarządzane tożsamości**. Upewnij się, że opcja **register with Azure Active Directory** ma wartość **Yes (tak**). 
    ![Rejestrowanie w usłudze Azure Active Directory](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. W Azure Portal Otwórz usługę **magazyny kluczy** i wybierz magazyn kluczy, którego próbujesz użyć dla domeny niestandardowej.
1. Wybierz pozycję **zasady dostępu**i sprawdź, czy istnieje jednostka usługi odpowiadająca nazwie wystąpienia usługi API Management. Jeśli istnieje, wybierz jednostkę usługi i upewnij się, że ma ona uprawnienie **Get** wymienione w obszarze **uprawnienia klucza tajnego**.  
    ![Dodawanie zasad dostępu dla jednostki usługi](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Jeśli usługa API Management nie znajduje się na liście, wybierz pozycję **Dodaj zasady dostępu**, a następnie Utwórz następujące zasady dostępu:
    - **Konfiguruj z szablonu**: Brak
    - **Wybierz podmiot zabezpieczeń**: Wyszukaj nazwę usługi API Management, a następnie wybierz ją z listy.
    - **Uprawnienia klucza**: Brak
    - **Uprawnienia klucza tajnego**: Get
    - **Uprawnienia certyfikatów**: Brak
1. Wybierz **przycisk OK** , aby utworzyć zasady dostępu.
1. Wybierz **Zapisz** Aby zapisać zmiany.

Sprawdź, czy problem został rozwiązany. W tym celu spróbuj utworzyć domenę niestandardową w usłudze API Management przy użyciu certyfikatu Key Vault.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o usłudze API Management:

- Poznaj więcej [filmów wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) dotyczących API Management.
* Aby poznać inne sposoby zabezpieczenia usługi zaplecza, zobacz [uwierzytelnianie wzajemne certyfikatów](api-management-howto-mutual-certificates.md).

* [Utwórz wystąpienie usługi API Management](get-started-create-service-instance.md).
* [Zarządzanie pierwszym interfejsem API](import-and-publish.md).