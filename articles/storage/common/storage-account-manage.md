---
title: Zarządzanie ustawieniami konta magazynu w usłudze Azure Portal — Azure Storage | Microsoft Docs
description: Informacje na temat zarządzania ustawieniami konta magazynu w Azure Portal, w tym konfigurowania ustawień kontroli dostępu, ponownego generowania kluczy dostępu do konta, zmieniania warstwy dostępu lub modyfikowania typu replikacji używanego przez konto. Dowiedz się również, jak usunąć konto magazynu w portalu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 3c01cc870b20c8256b215eb700548e6cd69ad0d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748986"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Zarządzanie ustawieniami konta magazynu w Azure Portal

W [Azure Portal](https://portal.azure.com)są dostępne różne ustawienia dla konta magazynu. W tym artykule opisano niektóre z tych ustawień oraz sposób ich używania.

## <a name="access-control"></a>Kontrola dostępu

Usługa Azure Storage obsługuje autoryzację Azure Active Directory dla magazynu obiektów blob i magazynu kolejek za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat autoryzacji w usłudze Azure AD, zobacz temat [autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md).

Ustawienia **kontroli dostępu** w Azure Portal umożliwiają prostą metodę przypisywania ról RBAC do użytkowników, grup, podmiotów usługi i zarządzanych tożsamości. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [Zarządzanie prawami dostępu do danych obiektów blob i kolejek przy użyciu RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Tagi

Usługa Azure Storage obsługuje Azure Resource Manager Tagi do organizowania zasobów platformy Azure przy użyciu dostosowanej taksonomii. Możesz zastosować Tagi do kont magazynu, aby można było grupować je w ramach subskrypcji w sposób logiczny.

W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.

Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Klawisze dostępu

Podczas tworzenia konta magazynu platforma Azure generuje 2 512-bitowe klucze dostępu do konta magazynu. Te klucze mogą służyć do autoryzacji dostępu do konta magazynu za pośrednictwem klucza współużytkowanego. Możesz obrócić i ponownie wygenerować klucze bez przerw w działaniu aplikacji, a firma Microsoft zaleca, aby to zrobić regularnie.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Wyświetlanie kluczy konta i parametrów połączenia

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Generowanie ponowne kluczy dostępu

Firma Microsoft zaleca, aby okresowo generować klucze dostępu w celu zapewnienia bezpieczeństwa konta magazynu. Przypisane są dwa klucze dostępu, dzięki czemu można obrócić klucze. Podczas obracania kluczy upewnij się, że aplikacja utrzymuje dostęp do usługi Azure Storage w trakcie całego procesu. 

> [!WARNING]
> Ponowne generowanie kluczy dostępu może mieć wpływ na wszystkie aplikacje lub usługi platformy Azure, które są zależne od klucza konta magazynu. Wszyscy klienci korzystający z klucza konta do uzyskiwania dostępu do konta magazynu muszą zostać zaktualizowani, aby używać nowego klucza, w tym usług Media Services, chmury, pulpitu i aplikacji mobilnych, oraz graficznych aplikacji interfejsu użytkownika dla usługi Azure Storage, takich jak [Eksplorator usługi Azure Storage ](https://azure.microsoft.com/features/storage-explorer/).

Wykonaj ten proces, aby obrócić klucze konta magazynu:

1. Zaktualizuj parametry połączenia w kodzie aplikacji, aby użyć klucza pomocniczego.
2. Ponownie wygeneruj podstawowy klucz dostępu do konta magazynu. W bloku **klucze dostępu** w Azure Portal kliknij pozycję **Generuj ponownie Klucz1**, a następnie kliknij przycisk **tak** , aby potwierdzić, że chcesz wygenerować nowy klucz.
3. Zaktualizuj parametry połączenia w kodzie, wprowadzając odwołanie do nowego podstawowego klucza dostępu.
4. W ten sam sposób wygeneruj ponownie pomocniczy klucz dostępu.

## <a name="account-configuration"></a>Konfiguracja konta

Po utworzeniu konta magazynu można zmodyfikować jego konfigurację. Można na przykład zmienić sposób replikowania danych lub zmienić warstwę dostępu konta z gorąca na chłodna. W [Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu, a następnie Znajdź i kliknij pozycję **Konfiguracja** w obszarze **Ustawienia** , aby wyświetlić i/lub zmienić konfigurację konta.

Zmiana konfiguracji konta magazynu może spowodować powstanie dodatkowych kosztów. Aby uzyskać więcej informacji, zobacz stronę z [cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

Aby usunąć konto magazynu, z którego już nie korzystasz, przejdź do konta magazynu w witrynie [Azure Portal](https://portal.azure.com) i kliknij pozycję **Usuń**. Usunięcie konta magazynu powoduje usunięcie całego konta, w tym wszystkich danych na koncie.

> [!WARNING]
> Nie można przywrócić usuniętego konta magazynu ani odzyskać żadnej zawartości znajdującej się na koncie przed usunięciem. Zanim usuniesz konto, wykonaj kopię zapasową wszystkich danych, które chcesz zapisać. Dotyczy to również wszystkich zasobów na koncie — po usunięciu obiektu Blob, tabeli, kolejki lub pliku elementy te są trwale usuwane.
> 

Podczas próby usunięcia konta magazynu skojarzonego z maszyną wirtualną platformy Azure może zostać wyświetlony komunikat o błędzie dotyczący wciąż używanego konta magazynu. Aby uzyskać pomoc w rozwiązaniu tego problemu, zobacz [Rozwiązywanie problemów związanych z błędami podczas usuwania kont magazynu](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Następne kroki

- [Omówienie konta usługi Azure Storage](storage-account-overview.md)
- [Tworzenie konta magazynu](storage-quickstart-create-account.md)
