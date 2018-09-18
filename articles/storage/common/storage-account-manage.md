---
title: Zarządzanie ustawieniami konta magazynu w witrynie Azure portal — usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać ustawienia konta magazynu w witrynie Azure portal, w tym konfigurowania ustawień kontroli dostępu, trwa ponowne generowanie kluczy dostępu do konta, zmiana warstwy dostępu lub modyfikowania typ replikacji używane przez konto. Poznasz również sposób usuwania konta magazynu w portalu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 5237be9fd75edc8abf5c5fa043574e8c2deb79e9
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740712"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Zarządzanie ustawieniami konta magazynu w witrynie Azure portal

Różne ustawienia dla konta magazynu są dostępne w [witryny Azure portal](https://portal.azure.com). W tym artykule opisano niektóre z tych ustawieniach i sposobach ich użycia.

## <a name="access-control"></a>Kontrola dostępu

Usługa Azure Storage obsługuje uwierzytelnianie przy użyciu usługi Azure Active Directory dla usługi Blob storage i Queue storage (wersja zapoznawcza) za pomocą kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą usługi Azure AD, zobacz [uwierzytelnienia dostępu do platformy Azure obiekty BLOB i kolejki, przy użyciu usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).

**Kontroli dostępu** ustawień w portalu Azure oferują prosty sposób Przypisz role RBAC do użytkowników, grup, nazw głównych usług i zarządzanych tożsamości. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [Zarządzaj praw dostępu do danych obiektów blob i kolejek przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

> [!NOTE]
> Uwierzytelnianie użytkowników lub aplikacji przy użyciu poświadczeń usługi Azure AD udostępnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z innej metody autoryzacji. Gdy możesz kontynuować klucza wspólnego autoryzacji za pomocą aplikacji, za pomocą usługi Azure AD zmierzone konieczności przechowywania klucza dostępu do konta za pomocą kodu. Nadal m ożna również udzielić szczegółową kontrolę dostępu do zasobów na koncie magazynu przy użyciu sygnatury dostępu współdzielonego (SAS), ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokeny sygnatur dostępu Współdzielonego ani martwić się o odwołaniu ze złamanymi zabezpieczeniami sygnatury dostępu Współdzielonego. 

## <a name="access-keys"></a>Klawisze dostępu

Podczas tworzenia konta magazynu platformy Azure generuje dwa klucze dostępu do konta magazynu 512-bitowe. Te klucze może służyć do autoryzowania dostępu do konta magazynu za pomocą klucza wspólnego. Można obracać i ponownie wygenerować klucze produktu bez przeszkód do aplikacji, a firma Microsoft zaleca się, że możesz to zrobić regularnie.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="view-and-copy-access-keys"></a>Wyświetlanie i kopiowanie kluczy dostępu

Aby wyświetlić swoje poświadczenia konta magazynu:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W sekcji **Ustawienia** omówienia kont magazynu wybierz pozycję **Klucze dostępu**. Zostaną wyświetlone klucze dostępu do Twojego konta, a także pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Klucz** w obszarze **key1** i kliknij przycisk **Kopiuj**, aby skopiować klucz konta.
5. Alternatywnie można skopiować ciąg całego połączenia. Znajdź wartość **Parametry połączenia** w obszarze **key1**i kliknij przycisk **Kopiuj**, aby skopiować parametry połączenia.

    ![Zrzut ekranu pokazujący sposób wyświetlić klucze dostępu w witrynie Azure portal](media/storage-manage-account/portal-connection-string.png)

### <a name="regenerate-access-keys"></a>Wygeneruj ponownie klucze dostępu

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
