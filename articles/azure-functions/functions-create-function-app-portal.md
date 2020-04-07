---
title: Tworzenie aplikacji funkcji z witryny Azure Portal
description: Utwórz nową aplikację funkcji na platformie Azure z portalu.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 6c17dc28d7eea4897759c1c832fdb1aba7f1e445
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756528"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Tworzenie aplikacji funkcji przy użyciu witryny Azure Portal

W tym temacie pokazano, jak używać usługi Azure Functions do tworzenia aplikacji funkcji w witrynie Azure portal. Aplikacja funkcji to kontener hostujący wykonywanie poszczególnych funkcji. 

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Po utworzeniu aplikacji funkcji możesz utworzyć indywidualne funkcje w jednym lub kilku różnych językach. Możesz tworzyć funkcje [za pomocą portalu](functions-create-first-azure-function.md#create-function), [ciągłego wdrażania](functions-continuous-deployment.md) lub [przekazywania przy użyciu protokołu FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Plany usługi

Usługa Azure Functions ma trzy różne plany usług: plan zużycia, plan Premium i plan dedykowany (usługa app service). Należy wybrać plan usługi podczas tworzenia aplikacji funkcji i nie można go następnie zmienić. Aby uzyskać więcej informacji, zobacz [Wybieranie planu hostingu usługi Azure Functions](functions-scale.md).

Jeśli planujesz uruchomić funkcje JavaScript na planie dedykowanym (app service), należy wybrać plan z mniejszą liczbą rdzeni. Aby uzyskać więcej informacji, zobacz [Dokumentacja języka JavaScript dla usługi Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Podczas tworzenia aplikacji funkcji, należy utworzyć lub łącze do ogólnego przeznaczenia konta usługi Azure Storage, który obsługuje blob, kolejki i magazynu tabel. Wewnętrznie usługa Functions używa usługi Storage na potrzeby operacji takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji. Niektóre konta magazynu nie obsługują kolejek i tabel, na przykład konta magazynu tylko dla obiektów blob, konta usługi Azure Premium Storage i konta magazynu ogólnego przeznaczenia z replikacją ZRS. Te konta są odfiltrowywane z bloku Konto magazynu podczas tworzenia aplikacji funkcji.

>[!NOTE]
>Podczas korzystania z planu hostingu Zużycie kod funkcji i pliki konfiguracji powiązania są przechowywane w usłudze Azure File Storage na głównym koncie magazynu. Po usunięciu głównego konta magazynu ta zawartość zostanie usunięta i nie będzie można jej odzyskać.

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [Wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Następne kroki

Portal Azure ułatwia tworzenie i wypróbowanie funkcji, ale zaleca się [lokalne tworzenie.](functions-develop-local.md) Po utworzeniu aplikacji funkcji w portalu nadal musisz dodać funkcję. 

> [!div class="nextstepaction"]
> [Dodawanie funkcji wyzwalanej http](functions-create-first-azure-function.md#create-function)
