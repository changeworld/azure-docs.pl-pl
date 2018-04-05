---
title: Generowanie certyfikatów infrastruktury kluczy publicznych stosu Azure stosu Azure zintegrowanych systemów wdrożenia | Dokumentacja firmy Microsoft
description: Zawiera opis systemów stosu Azure zintegrowanych processfor wdrażania certyfikatu PKI stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: fc2ec96113310f54d32a67ea5fa31725600046c9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Generowanie certyfikaty PKI dla wdrożenia usługi Azure stosu
Teraz, znając [wymagania dotyczące certyfikatu PKI](azure-stack-pki-certs.md) wdrożeń stosu Azure, należy uzyskać tych certyfikatów z urzędu certyfikacji, wybranych przez użytkownika. 

## <a name="request-certificates-using-an-inf-file"></a>Żądanie certyfikatów przy użyciu pliku INF
Jest jednym ze sposobów żądania certyfikatów z publicznego urzędu certyfikacji lub z wewnętrznego urzędu certyfikacji przy użyciu pliku INF. Narzędzie certreq.exe wbudowanych systemu Windows umożliwia określanie szczegółów certyfikatów, plik INF Generowanie pliku żądania zgodnie z opisem w tej sekcji. 

### <a name="sample-inf-file"></a>Przykładowy plik INF 
Plik INF żądania certyfikatu przykład można utworzyć pliku żądania certyfikatu w trybie offline do przesłania do urzędu certyfikacji (wewnętrzny lub publiczny). Plik INF obejmuje wszystkie wymagane punkty końcowe (w tym usługi opcjonalne PaaS) w certyfikacie jeden symbol wieloznaczny. 

Przykładowy plik INF przyjęto założenie, że region jest taki sam **sea** i wartości zewnętrznej nazwy FQDN jest **sea&#46;contoso&#46;com**. Zmiana tych wartości przed wygenerowaniem do danego środowiska. Plik INF dla danego wdrożenia. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Generowanie i prześlij żądanie do urzędu certyfikacji
Poniższy przepływ pracy w tym artykule opisano sposób dostosowywania i Użyj przykładowego pliku INF wygenerowany wcześniej, aby zażądać certyfikatu od urzędu certyfikacji:

1. **Edytuj i zapisuj pliku INF**. Kopiowanie próbki podane i zapisać go do nowego pliku tekstowego. Zamień wartości, która pasuje do wdrożenia i Zapisz plik jako nazwa podmiotu i nazwy FQDN zewnętrznej. Plik INF.
2. **Generuje żądanie przy użyciu certreq**. Przy użyciu komputera z systemem Windows, uruchom wiersz polecenia jako Administrator i uruchom następujące polecenie, aby wygenerować plik żądania (.req): `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Przesłania do urzędu certyfikacji**. Prześlij. Liczba ŻĄDAŃ pliku wygenerowane do urzędu certyfikacji (może być wewnętrznego lub publicznego).
4. **Import. CER**. Zwraca urzędu certyfikacji. Plik CER. Przy użyciu tego samego komputera z systemem Windows, z którego są generowane z pliku żądania, zaimportować. Plik CER zwrócona w magazynie komputera/personal. 
5. **Wyeksportować i skopiować. PFX do folderów wdrożenia**. Wyeksportuj certyfikat (łącznie z kluczem prywatnym) jako. PFX pliku i skopiować. Plik PFX do folderów wdrożenia opisanego w [wymagania dotyczące infrastruktury kluczy publicznych wdrażania stosu Azure](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Kolejne kroki
[Przygotuj certyfikaty PKI stosu Azure](azure-stack-prepare-pki-certs.md)
