---
title: Konta usługi Azure Storage
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć konto magazynu platformy Azure do użycia z usługą Azure Media Services.
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
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499840"
---
# <a name="azure-storage-accounts"></a>Konta usługi Azure Storage

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimediów na platformie Azure, musisz utworzyć konto usługi Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services.

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów transferu danych wychodzących.

Musisz mieć jedno **podstawowe** konto magazynu i możesz mieć dowolną liczbę **dodatkowych** kont magazynu skojarzonych z kontem usługi Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). Konta tylko obiektów Blob nie są dozwolone jako **podstawowe**.

Zalecamy korzystanie z GPv2, aby korzystać z najnowszych funkcji i wydajności. Aby dowiedzieć się więcej o kontach magazynu, zobacz [Omówienie konta usługi Azure Storage](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Tylko warstwa dostępu gorąca jest obsługiwana do użytku z usługą Azure Media Services, chociaż inne warstwy dostępu mogą służyć do zmniejszenia kosztów magazynowania zawartości, która nie jest aktywnie używana.

Istnieją różne jednostki SKU, które można wybrać dla konta magazynu. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU `--sku Standard_RAGRS`dla produkcji, należy wziąć pod uwagę , który zapewnia replikację geograficzną dla ciągłości biznesowej.

## <a name="assets-in-a-storage-account"></a>Zasoby na koncie magazynu

W umiaśnieniu usługi Media Services w wersji 3 interfejsy API magazynu są używane do przekazywania plików do zasobów. Aby uzyskać więcej informacji, zobacz [Zasoby w usłudze Azure Media Services w wersji 3](assets-concept.md).

> [!Note]
> Nie próbuj zmieniać zawartości kontenerów obiektów blob, które zostały wygenerowane przez zestawu SDK usług multimedialnych bez użycia interfejsów API usług Media Services.

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić zasoby w spoczynku, zasoby powinny być szyfrowane przez szyfrowanie po stronie magazynu. W poniższej tabeli pokazano, jak działa szyfrowanie po stronie magazynu w umiaźniach Media Services w wersji 3:

|Opcja szyfrowania|Opis|Media Services v3|
|---|---|---|
|Szyfrowanie magazynu usługi Media Services| Szyfrowanie AES-256, klucz zarządzany przez media services. |Nie jest obsługiwany. <sup>1.</sup>|
|[Szyfrowanie usługi przechowywania danych w stanie spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta.|Obsługiwane.|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w usłudze Key Vault.|Bez pomocy technicznej.|

<sup>1</sup> W umieniu Media Services w wersji 3 szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w celu zapewnienia zgodności z powrotem, gdy zasoby zostały utworzone za pomocą usługi Media Services w wersji 2, co oznacza, że wersja 3 współpracuje z istniejącymi zasobami zaszyfrowanymi magazynu, ale nie pozwala na tworzenie nowych.

## <a name="storage-account-errors"></a>Błędy konta magazynu

Stan „odłączone” dla konta usługi Media Services wskazuje, że konto nie ma już dostępu do co najmniej jednego dołączonego konta magazynu z powodu zmiany kluczy dostępu do magazynu. Aktualne klucze dostępu do magazynu są wymagane przez usługę Media Services do wykonywania wielu zadań na koncie.

Poniżej przedstawiono podstawowe scenariusze, które mogłyby spowodować, że konto usługi Media Services nie będzie mieć dostępu do dołączonych kont magazynu.

|Problem|Rozwiązanie|
|---|---|
|Konto usługi Media Services lub dołączone konta magazynu zostały poddane migracji do oddzielnych subskrypcji. |Migruj konta magazynu lub konta usługi Media Services, aby wszystkie były w tej samej subskrypcji. |
|Konto usługi Media Services używa dołączonego konta magazynu w innej subskrypcji, ponieważ było to wcześniejsze konto usługi Media Services, na którym było to obsługiwane. Wszystkie wczesne konta usługi Media Services zostały przekonwertowane na nowoczesne konta oparte na usłudze Azure Resources Manager i będą miały stan Rozłączony. |Migruj konto magazynu lub konto usługi Media Services, aby wszystkie były w tej samej subskrypcji.|

## <a name="azure-storage-firewall"></a>Zapora usługi Azure Storage

Usługa Azure Media Services nie obsługuje kont magazynu z włączoną zaporą usługi Azure Storage lub [prywatnymi punktami końcowymi.](https://docs.microsoft.com/azure/storage/common/storage-network-security)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dołączyć konto magazynu do konta usługi Media Services, zobacz [Tworzenie konta](create-account-cli-quickstart.md).
