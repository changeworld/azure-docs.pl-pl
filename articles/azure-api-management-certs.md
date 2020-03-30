---
title: Przekazywanie certyfikatu zarządzania usługami platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak przekazać certyfikat usługi Service Management dla witryny Azure portal.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 4b49a9b391eeca2d2e249b171d99f231bda6fdff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329123"
---
# <a name="upload-an-azure-service-management-certificate"></a>Przekazywanie certyfikatu zarządzania usługami platformy Azure
Certyfikaty zarządzania umożliwiają uwierzytelnianie przy użyciu klasycznego modelu wdrażania udostępnianego przez platformę Azure. Wiele programów i narzędzi (takich jak program Visual Studio lub zestaw Azure SDK) używa tych certyfikatów do zautomatyzowania konfigurowania i wdrażania różnych usług platformy Azure. 

> [!WARNING]
> Ostrożnie! Te typy certyfikatów umożliwiają każdemu, kto uwierzytelnia się z nimi, zarządzanie subskrypcją, z którą są skojarzone.
>
>

Jeśli chcesz uzyskać więcej informacji na temat certyfikatów platformy Azure (w tym tworzenie certyfikatu z podpisem własnym), zobacz [Omówienie certyfikatów dla usług Azure Cloud Services.](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)

Można również użyć [usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania kodu klienta na potrzeby automatyzacji.

**Uwaga:** Aby wykonać wszystkie operacje w obszarze Certyfikaty zarządzania, musisz być współadministratorem w ramach subskrypcji. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=849300) o dodaniu lub usunięciu współadministratorów z nowego portalu Azure 

## <a name="upload-a-management-certificate"></a>Przekazywanie certyfikatu zarządzania
Po utworzeniu certyfikatu zarządzania (plik cer tylko z kluczem publicznym) można przekazać go do portalu. Gdy certyfikat jest dostępny w portalu, każda osoba z pasującym certyfikatem (kluczem prywatnym) może łączyć się za pośrednictwem interfejsu API zarządzania i uzyskiwać dostęp do zasobów skojarzonej subskrypcji.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij **pozycję Wszystkie usługi** na dolnej liście usług platformy Azure, a następnie wybierz pozycję **Subskrypcje** w grupie Usługa _ogólne._

    ![Menu subskrypcji](./media/azure-api-management-certs/subscriptions_menu.png)

3. Upewnij się, że wybierz właściwą subskrypcję, którą chcesz skojarzyć z certyfikatem.     
4. Po wybraniu poprawnej subskrypcji naciśnij pozycję **Certyfikaty zarządzania** w grupie _Ustawienia._

    ![Ustawienia](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Naciśnij przycisk **Przesyłania.**

    ![Przekaż na stronie certyfikaty](./media/azure-api-management-certs/certificates_page.png)
6. Wypełnij informacje o oknie dialogowym i naciśnij przycisk **Przekaż**.

    ![Ustawienia](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz certyfikat zarządzania skojarzony z subskrypcją, możesz (po zainstalowaniu pasującego certyfikatu lokalnie) programowo połączyć się z [klasycznym interfejsem API REST modelu wdrażania](/azure/?pivot=sdkstools) i zautomatyzować różne zasoby platformy Azure, które są również skojarzone z tą subskrypcją.
