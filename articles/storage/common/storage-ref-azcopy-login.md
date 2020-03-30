---
title: azcopy zaloguj się | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia logowania azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295406"
---
# <a name="azcopy-login"></a>azcopy login

Loguje się do usługi Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

Zaloguj się do usługi Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Storage.

Aby autoryzować konto usługi Azure Storage, należy przypisać rolę **współautora danych obiektów blob magazynu** do konta użytkownika w kontekście konta magazynu, nadrzędnej grupy zasobów lub subskrypcji nadrzędnej.

To polecenie będzie buforować zaszyfrowane informacje logowania dla bieżącego użytkownika przy użyciu wbudowanych mechanizmów systemu operacyjnego.

Więcej informacji można znaleźć w przykładach.

> [!IMPORTANT]
> Jeśli ustawisz zmienną środowiskową przy użyciu wiersza polecenia, ta zmienna będzie czytelna w historii wiersza polecenia. Należy wziąć pod uwagę wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne z pojawiających się w historii, można użyć skryptu, aby monitować użytkownika o ich poświadczenia i ustawić zmienną środowiskową.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

Zaloguj się interaktywnie przy użyciu domyślnego identyfikatora dzierżawy usługi AAD ustawionego na wspólny:

```azcopy
azcopy login
```

Zaloguj się interaktywnie przy użyciu określonego identyfikatora dzierżawy:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Zaloguj się przy użyciu tożsamości maszyny wirtualnej przypisanej przez system:

```azcopy
azcopy login --identity
```

Zaloguj się przy użyciu tożsamości przypisanej przez użytkownika maszyny Wirtualnej i identyfikatora klienta tożsamości usługi:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Zaloguj się przy użyciu tożsamości przypisanej przez użytkownika maszyny Wirtualnej i identyfikatora obiektu tożsamości usługi:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Zaloguj się przy użyciu tożsamości przypisanej przez użytkownika maszyny Wirtualnej i identyfikatora zasobu tożsamości usługi:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Zaloguj się jako podmiot usługi przy użyciu klucza tajnego klienta. Ustaw zmienną środowiskową AZCOPY_SPA_CLIENT_SECRET do klucza tajnego dla jednostki usługi opartej na kluczu.

```azcopy
azcopy login --service-principal
```

Zaloguj się jako podmiot usługi przy użyciu certyfikatu i hasła. Ustaw zmienną środowiskową AZCOPY_SPA_CERT_PASSWORD na hasło certyfikatu dla autoryzacji głównej usługi opartej na cert.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Upewnij się, że /path/to/my/cert traktuje jako ścieżkę do pliku PEM lub PKCS12. AzCopy nie dociera do magazynu certyfikatów systemowych w celu uzyskania certyfikatu.

--certificate-path jest obowiązkowe podczas wykonywania jednostki usługi opartej na cert.

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--punkt końcowy aad|Punkt końcowy usługi Azure Active Directory do użycia. Wartość domyślna (`https://login.microsoftonline.com`) jest poprawna dla publicznej chmury platformy Azure. Ustaw ten parametr podczas uwierzytelniania w chmurze krajowej. Zobacz [punkty końcowe uwierzytelniania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Ta flaga nie jest potrzebna dla tożsamości usługi zarządzanej.|
|Ciąg --application-id|Identyfikator aplikacji tożsamości przypisanej przez użytkownika. Wymagane dla jednostki usługi eru.|
|Ciąg --certificate-path|Ścieżka do certyfikatu uwierzytelniania SPN. Wymagane dla jednostki usługi opartej na certyfikatach.|
|-h, --pomoc|Pokaż zawartość pomocy dla polecenia logowania.|
|--tożsamość|zaloguj się przy użyciu tożsamości maszyny wirtualnej, znanej również jako tożsamość usługi zarządzanej (MSI).|
|Ciąg --identity-client-id|Identyfikator klienta tożsamości przypisanej przez użytkownika.|
|Ciąg --identity-object-id|Identyfikator obiektu tożsamości przypisanej przez użytkownika.|
|Ciąg --identity-resource-id|Identyfikator zasobu tożsamości przypisanej przez użytkownika.|
|--service-zleceniodawca|Zaloguj się za pośrednictwem nazwy głównej usługi SPN przy użyciu certyfikatu lub klucza tajnego. Klucz tajny klienta lub hasło certyfikatu muszą być umieszczone w odpowiedniej zmiennej środowiskowej. Wpisz, `AzCopy env` aby wyświetlić nazwy i opisy zmiennych środowiskowych.|
|Ciąg --tenant-id| identyfikator dzierżawy usługi Azure active directory do użycia dla logowania interaktywnego urządzenia OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.|
|Ciąg typu --output|Format danych wyjściowych polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "tekst".|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
