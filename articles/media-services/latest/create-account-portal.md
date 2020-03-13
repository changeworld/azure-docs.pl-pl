---
title: Tworzenie konta usługi Azure Media Services za pomocą portalu Azure
description: Ten samouczek przeprowadzi Cię przez kroki tworzenia konta usługi Azure Media Services za pomocą portalu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5b05cd31a1747da0170556003e7a8534752e2fde
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137198"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Użyj Azure Portal, aby utworzyć konto Media Services

Azure Portal zapewnia sposób szybkiego tworzenia konta Azure Media Services. Korzystając z konta, możesz uzyskiwać dostęp do usługi Media Services, która umożliwia przechowywanie, szyfrowanie, kodowanie i przesyłanie strumieniowe zawartości oraz zarządzanie nią na platformie Azure.

Obecnie można użyć [Azure Portal](https://portal.azure.com/) , aby:

* Zarządzaj [zdarzeniami na żywo](live-events-outputs-concept.md)Media Services v3, 
* Wyświetl [zasoby](assets-concept.md)v3 (nie Zarządzaj), 
* [Uzyskaj informacje na temat uzyskiwania dostępu do interfejsów API](access-api-portal.md). 

W przypadku wszystkich innych zadań zarządzania (na przykład [transformacji i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości](content-protection-overview.md)) należy użyć [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

W tym artykule przedstawiono sposób tworzenia konta usługi Media Services przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz temat [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).
1. Kliknij pozycję **+ Utwórz zasób** > **nośnik** > **Media Services**.
1. W sekcji **Tworzenie konta Media Services** wprowadź wymagane wartości.
    
    | Name (Nazwa) | Opis |
    | ---|---|
    |**Nazwa konta**|Wprowadź nazwę nowego konta Media Services. Nazwa konta usługi Media Services składa się z małych liter i cyfr (bez spacji) i może zawierać od 3 do 24 znaków.|
    |**Subskrypcja**|Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z listy subskrypcji platformy Azure, do których masz dostęp.|
    |**Grupa zasobów**|Wybierz nowy lub istniejący zasób. Grupa zasobów jest kolekcją zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Więcej informacji można znaleźć [tutaj](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Lokalizacja**|Wybierz region geograficzny, który będzie używany do przechowywania multimediów i rekordów metadanych dla konta usługi Media Services. Ten region będzie służyć do przetwarzania i przesyłania strumieniowego multimediów. Na liście rozwijanej są wyświetlane tylko regiony dostępne w usłudze Media Services. |
    |**Konto magazynu**|Wybierz konto magazynu, aby dostarczyć magazyn obiektów BLOB zawartości multimedialnej z konta usługi Media Services. Można wybrać istniejące konto magazynu w tym samym regionie geograficznym co konto usługi Media Services albo utworzyć nowe konto magazynu. Nowe konto magazynu jest tworzone w tym samym regionie. Reguły dotyczące nazw kont magazynów są takie same, jak w przypadku kont usługi Media Services.<br/><br/>Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Za pomocą Azure Portal można dodać konta magazynu pomocniczego. Aby uzyskać więcej informacji, zobacz [konta usługi Azure Storage z kontami Azure Media Services](storage-account-concept.md).<br/><br/>Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.|
    
1. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, aby wyświetlić postęp wdrażania konta.
1. Kliknij opcję **Utwórz** w dolnej części formularza.

    Po utworzeniu konta usług Media Services do Twojego konta dodawany jest **domyślny** punkt końcowy przesyłania strumieniowego w stanie **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i [szyfrowania dynamicznego](content-protection-overview.md), punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi być w stanie **uruchomienia** . 

## <a name="next-steps"></a>Następne kroki

Jeśli planujesz uzyskać dostęp do interfejsu API Media Services programowo, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](access-api-portal.md).

## <a name="provide-feedback"></a>Przekaż opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

