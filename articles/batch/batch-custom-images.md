---
title: Aprowizuj pulę niestandardową z obrazu zarządzanego — usługa Azure Batch | Dokumenty firmy Microsoft
description: Utwórz pulę usługi Batch z zasobu zarządzanego obrazu, aby aprowizować węzły obliczeniowe za pomocą oprogramowania i danych dla aplikacji.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020152"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Tworzenie puli maszyn wirtualnych za pomocą obrazu zarządzanego

Aby utworzyć obraz niestandardowy dla maszyn wirtualnych puli usług wsadowych, można użyć [galerii obrazów udostępnionych](batch-sig-images.md)lub zasobu *zarządzanego obrazu.*

> [!TIP]
> W większości przypadków należy utworzyć obrazy niestandardowe przy użyciu Galerii obrazów udostępnionych. Korzystając z galerii obrazów udostępnionych, można aprowizować pule szybciej, skalować większe ilości maszyn wirtualnych i mieć większą niezawodność podczas inicjowania obsługi administracyjnej maszyn wirtualnych. Aby dowiedzieć się więcej, zobacz [Tworzenie puli niestandardowej za pomocą Galerii obrazów udostępnionych](batch-sig-images.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Zasób obrazu zarządzanego**. Aby utworzyć pulę maszyn wirtualnych przy użyciu obrazu niestandardowego, należy mieć lub utworzyć zasób obrazu zarządzanego w tej samej subskrypcji platformy Azure i regionie co konto usługi Batch. Obraz powinien być tworzony z migawek dysku systemu operacyjnego maszyny Wirtualnej i opcjonalnie jego dołączonych dysków z danymi. Aby uzyskać więcej informacji i kroków, aby przygotować obraz zarządzany, zobacz następującą sekcję.
  - Użyj unikatowego obrazu niestandardowego dla każdej utworzonej puli.
  - Aby utworzyć pulę z obrazem przy użyciu interfejsów API usługi Batch, należy `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`określić **identyfikator zasobu** obrazu, który jest formularzem . Aby użyć portalu, użyj **nazwy** obrazu.  
  - Zasób obrazu zarządzanego powinien istnieć przez cały okres istnienia puli, aby umożliwić skalowanie w górę i można go usunąć po usunięciu puli.

- **Uwierzytelnianie usługi Azure Active Directory (AAD).** Interfejs API klienta usługi Batch musi używać uwierzytelniania AAD. Obsługa usługi Azure Batch dla usługi AAD jest udokumentowana w [rozwiązaniach usługi uwierzytelniania wsadowego za pomocą usługi Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Przygotowywanie obrazu niestandardowego

Na platformie Azure można przygotować zarządzany obraz z:

- Migawki systemu operacyjnego i dysków danych maszyny Wirtualnej platformy Azure
- Uogólniona maszyna wirtualna platformy Azure z dyskami zarządzanymi
- Uogólniony lokalny dysk VHD przesłany do chmury

Aby skalować pule usługi Batch niezawodnie przy użyciu obrazu niestandardowego, zaleca się utworzenie obrazu zarządzanego przy użyciu *tylko* pierwszej metody: przy użyciu migawek dysków maszyny Wirtualnej. Zobacz następujące kroki, aby przygotować maszynę wirtualną, zrobić migawkę i utworzyć obraz z migawki.

### <a name="prepare-a-vm"></a>Przygotowywanie maszyny Wirtualnej

Jeśli tworzysz nową maszynę wirtualną dla obrazu, użyj obrazu pierwszej strony portalu Azure Marketplace obsługiwanego przez usługę Batch jako obrazu podstawowego dla obrazu zarządzanego. Tylko obrazy pierwszej strony mogą być używane jako obraz podstawowy. Aby uzyskać pełną listę odwołań do obrazów usługi Azure Marketplace obsługiwanych przez usługę Azure Batch, zobacz operację [jednostek SKU agenta węzła listy.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Nie można użyć obrazu innej firmy, który ma dodatkowe warunki licencji i zakupu jako obrazu podstawowego. Aby uzyskać informacje na temat tych obrazów w portalu Marketplace, zobacz wskazówki dotyczące maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) lub [Windows.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

- Upewnij się, że maszyna wirtualna jest tworzona z dyskiem zarządzanym. Jest to domyślne ustawienie magazynu podczas tworzenia maszyny Wirtualnej.
- Nie należy instalować rozszerzeń platformy Azure, takich jak rozszerzenie skryptu niestandardowego, na maszynie wirtualnej. Jeśli obraz zawiera wstępnie zainstalowane rozszerzenie, platforma Azure może napotkać problemy podczas wdrażania puli usługi Batch.
- Podczas korzystania z dołączonych dysków danych, należy zainstalować i sformatować dyski z wewnątrz maszyny Wirtualnej, aby z nich korzystać.
- Upewnij się, że obraz systemu operacyjnego, który podasz, używa domyślnego dysku tymczasowego. Agent węzła batch obecnie oczekuje domyślnego dysku tymczasowego.
- Gdy maszyna wirtualna jest uruchomiona, połącz się z nią za pośrednictwem protokołu RDP (dla windows) lub SSH (dla systemu Linux). Zainstaluj niezbędne oprogramowanie lub skopiuj żądane dane.  

### <a name="create-a-vm-snapshot"></a>Tworzenie migawki maszyny Wirtualnej

Migawka jest pełną, tylko do odczytu kopię dysku VHD. Aby utworzyć migawkę systemu operacyjnego maszyny Wirtualnej lub dysków z danymi, można użyć witryny Azure portal lub narzędzi wiersza polecenia. Aby uzyskać kroki i opcje tworzenia migawki, zobacz wskazówki dotyczące maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) lub [Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Tworzenie obrazu z jednej lub więcej migawek

Aby utworzyć obraz zarządzany z migawki, użyj narzędzi wiersza polecenia platformy Azure, takich jak polecenie [tworzenie obrazu az.](/cli/azure/image) Obraz można utworzyć, określając migawkę dysku systemu operacyjnego i opcjonalnie jedną lub więcej migawek dysku danych.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Tworzenie puli na podstawie niestandardowego obrazu w portalu

Po zapisaniu obrazu niestandardowego i znasz jego identyfikator lub nazwę zasobu, utwórz pulę usługi Batch z tego obrazu. Poniższe kroki pokazują, jak utworzyć pulę z witryny Azure portal.

> [!NOTE]
> Jeśli tworzysz pulę przy użyciu jednego z interfejsów API usługi Batch, upewnij się, że tożsamość używana do uwierzytelniania usługi AAD ma uprawnienia do zasobu obrazu. Zobacz [Uwierzytelniaj rozwiązania usługi batch z usługą Active Directory](batch-aad-auth.md).
>
> Zasób dla obrazu zarządzanego musi istnieć przez cały okres istnienia puli. Jeśli zasób bazowy zostanie usunięty, puli nie można skalować.

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi znajdować się w tej samej subskrypcji i regionie co grupa zasobów zawierająca obraz niestandardowy.
2. W oknie **Ustawienia** po lewej stronie wybierz pozycję menu **Baseny.**
3. W oknie **Pule** wybierz polecenie **Dodaj.**
4. W oknie **Dodawanie puli** wybierz pozycję **Obraz niestandardowy (Linux/Windows)** z listy rozwijanej **Typ obrazu.** Z **listy rozwijanej Niestandardowa maszyna wirtualna** wybierz nazwę obrazu (krótką formę identyfikatora zasobu).
5. Wybierz odpowiedni **wydawca/ofertę/sku** dla niestandardowego obrazu.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła,** **dedykowane węzły docelowe**i **węzły o niskim priorytecie,** a także wszelkie żądane ustawienia opcjonalne.

    Na przykład dla obrazu niestandardowego centrum danych systemu Microsoft Windows Server 2016 okno **Dodaj pulę** jest wyświetlane w sposób pokazany poniżej:

    ![Dodawanie puli z niestandardowego obrazu systemu Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Aby sprawdzić, czy istniejąca pula jest oparta na obrazie niestandardowym, zobacz właściwość **System operacyjny** w sekcji podsumowanie zasobów w oknie **Puli.** Jeśli pula została utworzona na podstawie obrazu niestandardowego, jest ustawiona na **Niestandardowy obraz maszyny Wirtualnej**.

Wszystkie niestandardowe obrazy skojarzone z pulą są wyświetlane w oknie **Właściwości** puli.

## <a name="considerations-for-large-pools"></a>Zagadnienia dotyczące dużych basenów

Jeśli planujesz utworzyć pulę z setkami maszyn wirtualnych lub więcej przy użyciu obrazu niestandardowego, ważne jest, aby postępować zgodnie z poprzednimi wskazówkami, aby użyć obrazu utworzonego na podstawie migawki maszyny Wirtualnej.

Należy również zwrócić uwagę na następujące kwestie:

- **Limity rozmiaru** — partia ogranicza rozmiar puli do 2500 dedykowanych węzłów obliczeniowych lub 1000 węzłów o niskim priorytecie podczas korzystania z obrazu niestandardowego.

  Jeśli używasz tego samego obrazu (lub wielu obrazów opartych na tej samej podstawowej migawki) do utworzenia wielu pul, całkowita liczba węzłów obliczeniowych w pulach nie może przekroczyć poprzednich limitów. Nie zaleca się używania obrazu lub jego podstawowej migawki dla więcej niż jednej puli.

  Limity mogą zostać zmniejszone, jeśli skonfigurujesz pulę [przychodzącej nat.](pool-endpoint-configuration.md)

- **Przeskaluj limit czasu** — jeśli pula zawiera stałą liczbę węzłów (nie jest skalowana automatycznie), zwiększ właściwość resmożna losowania puli do wartości, takiej jak 20-30 minut. Jeśli pula nie osiągnie rozmiaru docelowego w okresie limitu czasu, wykonaj [kolejną operację zmieniania rozmiaru](/rest/api/batchservice/pool/resize).

  Jeśli planujesz pulę z więcej niż 300 węzłów obliczeniowych, może być konieczne ponowne zmienić rozmiar puli wiele razy, aby osiągnąć rozmiar docelowy.
  
Korzystając z [galerii obrazów udostępnionych,](batch-sig-images.md)można tworzyć większe pule z dostosowanymi obrazami wraz z większą liczoną repliką obrazów udostępnionych. Przy użyciu obrazów udostępnionych czas potrzebny do puli, aby osiągnąć stan stacjonarny jest do 25% szybsze, a opóźnienie bezczynności maszyny Wirtualnej jest do 30% krótszy.

## <a name="considerations-for-using-packer"></a>Uwagi dotyczące korzystania z packera

Tworzenie zasobu zarządzanego obrazu bezpośrednio za pomocą programu Packer można wykonać tylko za pomocą kont w trybie subskrypcji użytkownika wsadowych. W przypadku kont trybu usługi wsadowej należy najpierw utworzyć dysk VHD, a następnie zaimportować dysk VHD do zasobu zarządzanego obrazu. W zależności od trybu alokacji puli (subskrypcja użytkownika lub usługa wsadowa) kroki tworzenia zasobu zarządzanego obrazu będą się różnić.

Upewnij się, że zasób użyty do utworzenia obrazu zarządzanego istnieje przez okres istnienia dowolnej puli odwołującej się do obrazu niestandardowego. Nie można tego zrobić może spowodować błędy alokacji puli i/lub błędy rozmiaru.

Jeśli obraz lub zasób bazowy zostanie usunięty, `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`może pojawić się błąd podobny do: . Jeśli zostanie wyświetlony ten błąd, upewnij się, że zasób źródłowy nie został usunięty.

Aby uzyskać więcej informacji na temat tworzenia maszyny Wirtualnej przy użyciu programu Packer, zobacz [Tworzenie obrazu systemu Linux za pomocą programu Packer](../virtual-machines/linux/build-image-with-packer.md) lub Tworzenie obrazu systemu Windows za pomocą programu [Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowy przegląd usługi Batch, zobacz [Tworzenie dużych równoległych rozwiązań obliczeniowych za pomocą usługi Batch](batch-api-basics.md).
