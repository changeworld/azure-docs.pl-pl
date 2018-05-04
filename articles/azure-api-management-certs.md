---
title: Przekaż certyfikat zarządzania usługi Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przekazać certyfikatu usługi zarządzania dla portalu Azure.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: be548a8e823d4c7109951183886764738a66ecea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="upload-an-azure-service-management-certificate"></a>Przekaż certyfikat zarządzania usługi Azure
Certyfikaty zarządzania umożliwiają uwierzytelniania za pomocą klasycznego modelu wdrażania dostarczany przez platformę Azure. Wiele programów i narzędzia (np. programu Visual Studio lub zestawu Azure SDK) korzystały z tych certyfikatów można zautomatyzować konfigurowanie i wdrażanie różnych usług platformy Azure. 

> [!WARNING]
> Ostrożnie. Te typy certyfikatów umożliwiają każdego, kto jest uwierzytelniany w usłudze ich do zarządzania subskrypcją, które są skojarzone.
>
>

Jeśli chcesz więcej informacji o certyfikatach Azure (takie jak tworzenie certyfikatu z podpisem własnym), zobacz [Omówienie certyfikatów dla usług w chmurze Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Można również użyć [usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania kodu klienta dla celów automatyzacji.

**Uwaga:** musi być współadministratorem subskrypcji wykonywanie żadnych operacji w obszarze certyfikaty zarządzania. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=849300) o tym, jak dodać lub usunąć ko-Administratorzy z nowego portalu Azure 

## <a name="upload-a-management-certificate"></a>Przekazywanie certyfikatu zarządzania
Po utworzeniu utworzony certyfikat zarządzania, (plik cer z kluczem publicznym) można przekazać go do portalu. Jeśli certyfikat jest dostępny w portalu, każda osoba mająca zgodnego certyfikatu (klucz prywatny) łączenie się za pośrednictwem interfejsu API zarządzania i dostęp do zasobów w subskrypcji skojarzone.

1. Zaloguj się do witryny [Azure Portal](http://portal.azure.com).
2. Kliknij przycisk **wszystkie usługi** na dolnej liście usługi Azure, następnie wybierz **subskrypcje** w _ogólne_ grupy usług.

    ![Menu subskrypcji](./media/azure-api-management-certs/subscriptions_menu.png)

3. Upewnij się wybrać poprawny subskrypcji, która ma być skojarzony z certyfikatem.     
4. Po wybraniu subskrypcji poprawny, naciśnij klawisz **certyfikaty zarządzania** w _ustawienia_ grupy.

    ![Ustawienia](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Naciśnij klawisz **przekazać** przycisku.

    ![Przekaż na stronie certyfikatów](./media/azure-api-management-certs/certificates_page.png)
6. Wypełnij okno dialogowe i naciśnij klawisz **przekazać**.

    ![Ustawienia](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz certyfikat zarządzania skojarzony z subskrypcją można (po zainstalowaniu zgodnego certyfikatu lokalnie) programowo nawiązać [klasycznego modelu wdrażania interfejsu API REST](https://msdn.microsoft.com/library/azure/mt420159.aspx) i automatyzacji różnych zasobów Azure, które są również powiązaną z jego subskrypcją.
