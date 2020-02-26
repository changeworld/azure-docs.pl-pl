---
title: Konta usługi Azure Storage z kontami Azure Media Services | Microsoft Docs
description: Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 4bbadd7e10f0fd6896932dd79a5ca42d9906d2a2
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602133"
---
# <a name="azure-storage-accounts"></a>Konta usługi Azure Storage

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. 

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu w tej samej lokalizacji co konto Media Services, aby uniknąć dodatkowych kosztów opóźnienia i danych wyjściowych.

Musisz mieć jedno **główne** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z Twoim kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). <br/>Konta tylko obiektów blob nie są dozwolone jako **główne**. 

Zalecamy korzystanie z programu GPv2, dzięki czemu można korzystać z najnowszych funkcji i wydajności. Aby dowiedzieć się więcej na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Tylko Warstwa dostępu gorąca jest obsługiwana w przypadku Azure Media Services, ale inne warstwy dostępu mogą służyć do obniżenia kosztów magazynowania zawartości, która nie jest aktywnie używana.

Istnieją różne jednostki SKU, które można wybrać dla konta magazynu. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU dla środowiska produkcyjnego weź pod uwagę użycie parametru `--sku Standard_RAGRS`, co zapewnia replikację geograficzną na potrzeby zapewnienia ciągłości działania. 

## <a name="assets-in-a-storage-account"></a>Zasoby na koncie magazynu

W Media Services v3 interfejsy API magazynu są używane do przekazywania plików do zasobów. Aby uzyskać więcej informacji, zobacz temat [koncepcje zasobów](assets-concept.md).

> [!Note]
> Nie należy próbować zmieniać zawartości kontenerów obiektów blob, które zostały wygenerowane przez zestaw SDK Media Services bez używania Media Services interfejsów API.
 
## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w wersji 3 usługa Media Services:

|Opcja szyfrowania|Opis|Media Services v3|
|---|---|---|
|Szyfrowanie magazynu usługi Media Services| AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Nieobsługiwane<sup>(1)</sup>|
|[szyfrowanie usługi Storage danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|

<sup>1</sup> w Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w przypadku zgodności z poprzednimi wersjami, gdy zasoby zostały utworzone przy użyciu Media Services V2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="storage-account-errors"></a>Błędy konta magazynu

Stan „odłączone” dla konta usługi Media Services wskazuje, że konto nie ma już dostępu do co najmniej jednego dołączonego konta magazynu z powodu zmiany kluczy dostępu do magazynu. Aktualne klucze dostępu do magazynu są wymagane przez usługę Media Services do wykonywania wielu zadań na koncie.

Poniżej przedstawiono podstawowe scenariusze, które mogłyby spowodować, że konto usługi Media Services nie będzie mieć dostępu do dołączonych kont magazynu. 

|Problem|Rozwiązanie|
|---|---|
|Konto usługi Media Services lub dołączone konta magazynu zostały poddane migracji do oddzielnych subskrypcji. |Przeprowadź migrację kont magazynu lub konta usługi Media Services, aby wszystkie były w tej samej subskrypcji. |
|Konto usługi Media Services używa dołączonego konta magazynu w innej subskrypcji, ponieważ było to wcześniejsze konto usługi Media Services, na którym było to obsługiwane. Wszystkie wcześniejsze konta usługi Media Services zostały przekonwertowane na nowoczesne konta platformy Azure Resource Manager (ARM) i są w stanie „odłączone”. |Przeprowadź migrację kont magazynu lub konta usługi Media Services, aby wszystkie były w tej samej subskrypcji.|

## <a name="azure-storage-firewall"></a>Zapora usługi Azure Storage

Azure Media Services nie obsługuje kont magazynu z włączoną zaporą usługi Azure Storage lub [prywatnymi punktami końcowymi](https://docs.microsoft.com/azure/storage/common/storage-network-security) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dołączyć konto magazynu do konta Media Services, zobacz [Tworzenie konta](create-account-cli-quickstart.md).
