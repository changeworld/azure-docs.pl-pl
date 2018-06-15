---
title: Wersje usługi Key Vault
description: Różne wersje usługi Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012138"
---
# <a name="key-vault-versions"></a>Wersje usługi Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - klucze konta magazynu zarządzanego

2017 lato — ułatwia integrację z usługą Azure Storage dodawana funkcja klucze konta magazynu. Zobacz temat Omówienie, aby uzyskać więcej informacji, [zarządzane klucze konta magazynu — omówienie](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01-soft-delete

2017 lato - funkcji usuwania nietrwałego dodane do ochrony danych ulepszone magazynów kluczy i magazyn kluczy obiektów. Zobacz temat Omówienie, aby uzyskać więcej informacji, [omówienie usuwania nietrwałego](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - zarządzania certyfikatami

Zarządzanie certyfikatami są dodawane jako funkcję z wersją GA 2015-06-01 na 26 września 2016 roku.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - ogólnej dostępności

Ogólnodostępnej wersji 2015-06-01, ogłaszane na 24 czerwiec 2015 r.

W tej wersji wprowadzono następujące zmiany:

- Usuwanie klucza — "Użyj" pole usunięte.
- Pobierz informacje o kluczu — "Użyj" pola usunięte.
- Importuj klucz do magazynu — "Użyj" pole usunięte.
- Przywróć klucz — "Użyj" pole usunięte.
- Zmienione "RSA_OAEP" do "RSA-OAEP" algorytmów RSA. Zobacz [o kluczy, kluczy tajnych i certyfikaty](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Drugi preview wersji 2015-02-01-preview, ogłaszane na 20 kwietnia 2015 r. Aby uzyskać więcej informacji, zobacz [aktualizacji interfejsu API REST](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) wpis w blogu.

Zaktualizowano następujące zadania:

- Utwórz listę kluczy w magazynie — dodano podział na strony obsługi operacji.
- Utwórz listę wersji klucza — dodano operację, aby wyświetlić listę wersji klucza.
- Utwórz listę kluczy tajnych w magazynie — dodano podział na strony pomocy technicznej.
- Wyświetl listę wersji klucza tajnego — Dodaj operację, aby wyświetlić listę wersji klucza tajnego.
- Wszystkie operacje — dodano utworzone/zaktualizowane sygnatury czasowe do atrybutów.
- Utwórz klucz tajny - Content-Type są dodawane do kluczy tajnych.
- Utwórz klucz — dodaje znaczniki jako opcjonalne informacje.
- Utwórz klucz tajny - dodaje znaczniki jako opcjonalne informacje.
- Zaktualizuj klucz — dodaje znaczniki jako opcjonalne informacje.
- Zaktualizuj klucz tajny - dodaje znaczniki jako opcjonalne informacje.
- Zmienić rozmiar maksymalny dla kluczy tajnych z 10 tys do 25 KB. Zobacz, [o kluczy, kluczy tajnych i certyfikaty](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-preview

Pierwszy preview wersji 2014-12-08-preview, ogłaszane na 8 stycznia 2015 r.

## <a name="see-also"></a>Zobacz także
- [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
