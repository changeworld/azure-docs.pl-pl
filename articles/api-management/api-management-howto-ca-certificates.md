---
title: Dodawanie niestandardowego certyfikatu urzędu certyfikacji — usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać niestandardowy certyfikat urzędu certyfikacji w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: b05713fd8acb49b25dba04781c1049c9f05115fb
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444697"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Jak dodać niestandardowy certyfikat urzędu certyfikacji w usłudze Azure API Management

Usługa Azure API Management umożliwia instalowanie certyfikatów urzędu certyfikacji na komputerze wewnątrz zaufany główny urząd certyfikacji i magazyny certyfikatów pośrednich. Tej funkcji należy używać, jeśli usługi wymagają niestandardowego certyfikatu urzędu certyfikacji.

Artykuł pokazuje, jak zarządzać certyfikatami urzędu certyfikacji wystąpienia usługi Azure API Management w witrynie Azure portal.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Przekaż certyfikat urzędu certyfikacji

![Dodaj certyfikaty urzędu certyfikacji](media/api-management-howto-ca-certificates/00.png)

Wykonaj poniższe kroki, aby przekazać nowy certyfikat urzędu certyfikacji. Jeśli nie utworzono jeszcze wystąpienie usługi API Management, zapoznaj się z samouczkiem [Tworzenie wystąpienia usługi API Management](get-started-create-service-instance.md).

1. Przejdź do wystąpienia usługi Azure API Management w witrynie Azure portal.

2. Wybierz **certyfikatów urzędu certyfikacji** z menu.

3. Kliknij przycisk **+ Dodaj** przycisku.  

    ![Dodaj certyfikaty urzędu certyfikacji](media/api-management-howto-ca-certificates/01.png)  

4. Przeglądaj w poszukiwaniu certyfikatu, a następnie decyduj w magazynie certyfikatów. Tylko klucz publiczny jest potrzebny, więc nie jest wymagane hasło.

    ![Dodaj certyfikaty urzędu certyfikacji](media/api-management-howto-ca-certificates/02.png)  

5. Kliknij pozycję **Zapisz**. Ta operacja może potrwać kilka minut.

    ![Dodaj certyfikaty urzędu certyfikacji](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Można przekazać certyfikatu urzędu certyfikacji za pomocą `New-AzureRmApiManagementSystemCertificate` polecenia programu Powershell.

## <a name="step1a"> </a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, kliknij przycisk menu kontekstowego **...**  i wybierz **Usuń** obok certyfikatu.

![Usuń certyfikaty urzędu certyfikacji](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a