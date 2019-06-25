---
title: Zarządzanie ustawieniami konta magazynu w witrynie Azure portal — usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać ustawienia konta magazynu w witrynie Azure portal, w tym konfigurowania ustawień kontroli dostępu, trwa ponowne generowanie kluczy dostępu do konta, zmiana warstwy dostępu lub modyfikowania typ replikacji używane przez konto. Poznasz również sposób usuwania konta magazynu w portalu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 66bdc4bd1e17347419a6eccd7c9532db17b33001
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303493"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Zarządzanie ustawieniami konta magazynu w witrynie Azure portal

Różne ustawienia dla konta magazynu są dostępne w [witryny Azure portal](https://portal.azure.com). W tym artykule opisano niektóre z tych ustawieniach i sposobach ich użycia.

## <a name="access-control"></a>Kontrola dostępu

Usługa Azure Storage obsługuje autoryzacji w usłudze Azure Active Directory dla usługi Blob storage i Queue storage przy użyciu kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji o autoryzacji w usłudze Azure AD, zobacz [Autoryzuj dostęp do platformy Azure, obiekty BLOB i kolejki przy użyciu usługi Azure Active Directory](storage-auth-aad.md).

**Kontroli dostępu** ustawień w portalu Azure oferują prosty sposób Przypisz role RBAC do użytkowników, grup, nazw głównych usług i zarządzanych tożsamości. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [Zarządzaj praw dostępu do danych obiektów blob i kolejek przy użyciu RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>`Tags`

Usługa Azure Storage obsługuje tagi usługi Azure Resource Manager służący do organizowania zasobów platformy Azure dostosowanych taksonomii. Znaczniki można dodawać do swoich kont magazynu, dzięki czemu grupy w ramach Twojej subskrypcji w logiczny sposób.

W przypadku kont magazynu Nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.

Aby uzyskać więcej informacji, zobacz [organizowania zasobów platformy Azure za pomocą tagów](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Klucze dostępu

Podczas tworzenia konta magazynu platformy Azure generuje dwa klucze dostępu do konta magazynu 512-bitowe. Te klucze może służyć do autoryzowania dostępu do konta magazynu za pomocą klucza wspólnego. Można obracać i ponownie wygenerować klucze produktu bez przeszkód do aplikacji, a firma Microsoft zaleca się, że możesz to zrobić regularnie.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Wyświetl klucze konta i parametry połączenia

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Firma Microsoft zaleca ponowne generowanie kluczy dostępu okresowo, aby zabezpieczyć swoje konto magazynu. Są przypisywane dwa klucze dostępu, tak, aby obrócić klucze. Obracania klucze pozwala mieć pewność, że aplikacja obsługuje dostęp do usługi Azure Storage w całym procesie. 

> [!WARNING]
> Trwa ponowne generowanie kluczy dostępu może mieć wpływ na wszystkie aplikacje lub usługi platformy Azure, które są zależne od klucza konta magazynu. Należy zaktualizować żadnych klientów, którzy używają klucza konta dostępu do konta magazynu, aby używać nowego klucza, w tym usługi media services, cloud, pulpitu i aplikacje mobilne i aplikacje interfejsu graficznego dla usługi Azure Storage, takie jak [platformy Azure Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/).

Postępuj zgodnie z tego procesu wymiany kluczy konta magazynu:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby używać klucza pomocniczego.
2. Ponownie wygeneruj podstawowy klucz dostępu do konta magazynu. Na **klucze dostępu** bloku w witrynie Azure portal kliknij pozycję **Generuj ponownie klucz1**, a następnie kliknij przycisk **tak** aby upewnić się, że chcesz wygenerować nowy klucz.
3. Zaktualizuj parametry połączenia w kodzie, wprowadzając odwołanie do nowego podstawowego klucza dostępu.
4. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu.

## <a name="account-configuration"></a>Konfiguracja konta

Po utworzeniu konta magazynu, można zmodyfikować jego konfigurację. Na przykład można zmienić, jak Twoje dane są replikowane lub zmienić warstwę dostępu do konta z gorąca na chłodna. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu, a następnie znajdź i kliknij przycisk **konfiguracji** w obszarze **ustawienia** Aby wyświetlić lub zmienić konfigurację konta.

Zmienianie konfiguracji konta magazynu może spowodować dodano kosztów. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) strony.

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

Aby usunąć konto magazynu, z którego już nie korzystasz, przejdź do konta magazynu w witrynie [Azure Portal](https://portal.azure.com) i kliknij pozycję **Usuń**. Usunięcie konta magazynu powoduje usunięcie całego konta, w tym wszystkich danych na koncie.

> [!WARNING]
> Nie można przywrócić usuniętego konta magazynu ani odzyskać żadnej zawartości znajdującej się na koncie przed usunięciem. Zanim usuniesz konto, wykonaj kopię zapasową wszystkich danych, które chcesz zapisać. Dotyczy to również wszystkich zasobów na koncie — po usunięciu obiektu Blob, tabeli, kolejki lub pliku elementy te są trwale usuwane.
> 

Podczas próby usunięcia konta magazynu skojarzonego z maszyną wirtualną platformy Azure może zostać wyświetlony komunikat o błędzie dotyczący wciąż używanego konta magazynu. Aby uzyskać pomoc w rozwiązaniu tego problemu, zobacz [Rozwiązywanie problemów związanych z błędami podczas usuwania kont magazynu](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Kolejne kroki

- [Przegląd konta usługi Azure storage](storage-account-overview.md)
- [Tworzenie konta magazynu](storage-quickstart-create-account.md)
