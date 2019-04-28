---
title: Tworzenie aplikacji funkcji przy użyciu witryny Azure Portal | Microsoft Docs
description: Utwórz nową aplikację funkcji w usłudze Azure App Service z poziomu portalu.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: a1d4af695807a6e2c5ef4ee74527083002bc6015
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342443"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Tworzenie aplikacji funkcji przy użyciu witryny Azure Portal

Aplikacje funkcji platformy Azure korzystają z infrastruktury usługi Azure App Service. W tym temacie opisano sposób tworzenia aplikacji funkcji w witrynie Azure Portal. Aplikacja funkcji to kontener hostujący wykonywanie poszczególnych funkcji. Po utworzeniu aplikacji funkcji w planie hostingu usługi App Service aplikacja ta może korzystać ze wszystkich funkcji usługi App Service.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Podczas tworzenia aplikacji funkcji podaj prawidłową **nazwę aplikacji**, która może zawierać tylko litery, cyfry i łączniki. Podkreślenie (**_**) nie jest dozwolonym znakiem.

Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. 

Po utworzeniu aplikacji funkcji możesz utworzyć indywidualne funkcje w jednym lub kilku różnych językach. Możesz tworzyć funkcje [za pomocą portalu](functions-create-first-azure-function.md#create-function), [ciągłego wdrażania](functions-continuous-deployment.md) lub [przekazywania przy użyciu protokołu FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Plany usługi

Usługa Azure Functions oferuje dwa różne plany usługi: Plan zużycia i plan usługi App Service. W planie Zużycie moc obliczeniowa jest automatycznie alokowana, gdy uruchomiony jest Twój kod, oraz jest skalowana w poziomie na potrzeby obsługi obciążenia, a następnie skalowana w pionie, gdy kod nie jest uruchomiony. Plan usługi App Service daje aplikacji funkcji dostęp do wszystkich funkcji usługi App Service. Musisz wybrać plan podczas tworzenia aplikacji funkcji i obecnie nie można go zmienić. Aby uzyskać więcej informacji, zobacz [Wybieranie planu hostingu usługi Azure Functions](functions-scale.md).

Jeśli zamierzasz uruchamiać funkcje w języku JavaScript w planie usługi App Service, wybierz plan z mniejszą liczbą rdzeni. Aby uzyskać więcej informacji, zobacz [Dokumentacja języka JavaScript dla usługi Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Podczas tworzenia aplikacji funkcji w usłudze App Service należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia obsługujące usługi Blob Storage, Queue Storage i Table Storage lub połączyć takie konto. Wewnętrznie usługa Functions używa usługi Storage na potrzeby operacji takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji. Niektóre konta magazynu nie obsługują kolejek i tabel, na przykład konta magazynu tylko dla obiektów blob, konta usługi Azure Premium Storage i konta magazynu ogólnego przeznaczenia z replikacją ZRS. Te konta są odfiltrowywane z bloku Konto magazynu podczas tworzenia aplikacji funkcji.

>[!NOTE]
>Podczas korzystania z planu hostingu Zużycie kod funkcji i pliki konfiguracji powiązania są przechowywane w usłudze Azure File Storage na głównym koncie magazynu. Po usunięciu głównego konta magazynu ta zawartość zostanie usunięta i nie będzie można jej odzyskać.

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [Wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]
