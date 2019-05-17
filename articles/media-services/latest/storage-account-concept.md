---
title: Przekazywanie i magazynu przy użyciu usługi Azure Media Services chmurze | Dokumentacja firmy Microsoft
description: Ten artykuł chmury przekazywania i kwestie dotyczące magazynu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: 9cbb995eb3310a2263185d6fd6dba20efce37f38
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550143"
---
# <a name="cloud-upload-and-storage"></a>Przekazywanie do chmury i magazynowanie w niej

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto usługi Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. 

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych kosztów opóźnienia i danych wychodzących

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). 

>[!NOTE]
> Konta tylko obiektów blob nie są dozwolone jako **główne**. 

Firma Microsoft zaleca korzystanie z kont GPv2, aby można było korzystać z zalet wybór między gorąca i warstwy magazynowania chłodna. Aby dowiedzieć się więcej na temat kont magazynu, zobacz [Przegląd konta usługi Azure Storage](../../storage/common/storage-account-overview.md). 

Istnieją różne jednostki SKU, można wybrać konta magazynu. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU dla środowiska produkcyjnego weź pod uwagę użycie parametru `--sku Standard_RAGRS`, co zapewnia replikację geograficzną na potrzeby zapewnienia ciągłości działania. 

## <a name="assets-in-a-storage-account"></a>Zasoby w ramach konta magazynu

W Media Services v3 interfejsów API magazynu są używane do przekazywania plików do zasobów. Aby uzyskać więcej informacji, zobacz [koncepcji zasoby](assets-concept.md).

> [!Note]
> Nie należy próbować zmienić zawartość kontenerów obiektów blob, które zostały wygenerowane przez zestaw SDK usług Media Services bez korzystania z interfejsów API usług Media Services.
 
## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w wersji 3 usługa Media Services:

|Opcja szyfrowania|Opis|Media Services v3|
|---|---|---|
|Szyfrowanie magazynu usługi Media Services| AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Nieobsługiwane<sup>(1)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|

<sup>1</sup> Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="storage-account-errors"></a>Błędy konta magazynu

Stan "Disconnected" dla konta usługi Media Services wskazuje, że konto ma już dostęp do jednego lub większej liczby kont dołączonego magazynu z powodu zmiany kluczy dostępu do magazynu. Klucze dostępu do magazynu aktualne są wymagane przez usługę Media Services do wykonywania wielu zadań w ramach konta.

Poniżej przedstawiono podstawowe scenariusze, które mogłyby spowodować konto usługi Media Services nie mają dostępu do kont magazynu dołączone. 

|Problem|Rozwiązanie|
|---|---|
|Konto usługi Media Services lub kont magazynu dołączone zostały poddane migracji do oddzielania subskrypcji. |Migrowanie kont magazynu lub konta usługi Media Services, aby były one w tej samej subskrypcji. |
|Konto usługi Media Services używa dołączone konto magazynu w innej subskrypcji, jak konto usługi Media Services wczesne gdzie to obsługiwane. Wszystkie wcześniejsze konta usługi Media Services zostały przekonwertowane na nowoczesnych konta platformy Azure zasoby Manager (ARM) na podstawie, zostanie rozłączony. |Migruj konto magazynu lub konta usługi Media Services, aby były one w tej samej subskrypcji.|

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak dołączyć konto magazynu do konta usługi Media Services, zobacz [Tworzenie konta usługi](create-account-cli-quickstart.md).
