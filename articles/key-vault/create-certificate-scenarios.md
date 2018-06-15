---
title: Monitorowanie i zarządzanie nimi Tworzenie certyfikatu
description: Scenariusze prezentacja szereg opcji tworzenia, monitorowania i interakcji z tworzenia certyfikatu przetwarzać z magazynu kluczy.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: e1ea77304fa59b67e0e28a4c7e0b13633eeeff6f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012150"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorowanie i zarządzanie nimi Tworzenie certyfikatu
Dotyczy: Azure  

Poniżej 

Scenariusze / działania opisane w tym artykule są:

- Żądanie certyfikatu KV z obsługiwanych wystawcą
- Pobierz oczekującego żądania — stan żądania to "w toku"
- Pobierz oczekującego żądania — stan żądania to "ukończone"
- Pobierz oczekującego żądania - oczekujące żądania stanu "anulowania" lub "nie powiodło się"
- Pobierz oczekującego żądania - oczekujące żądania stanu jest "usunięty" lub "zastąpione"
- Utwórz (lub zaimportować) podczas oczekującego żądania istnieje — "w toku" jest w stanie
- Scalanie podczas oczekującego żądania jest tworzony z wystawcą (na przykład DigiCert)
- Żądania anulowania, gdy stan oczekującego żądania to "w toku"
- Usuwanie obiektu oczekującego żądania
- Ręcznie Utwórz certyfikat KV
- Scalanie po utworzeniu żądania oczekującego — utworzenie ręcznego certyfikatu

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Żądanie certyfikatu KV z obsługiwanych wystawcą 

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

Poniższe przykłady wymagają obiektu o nazwie "mydigicert", aby być już dostępne w magazynie kluczy z dostawcą wystawcy jako DigiCert. Aby uzyskać więcej informacji na temat pracy z wystawców, zobacz [wystawców certyfikatów](/rest/api/keyvault/certificate-issuers.md).  

### <a name="request"></a>Żądanie  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert",  
    "cty": "OV-SSL",  
    }  
  }  
}  

```  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "mydigicert"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "InProgress",  
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-inprogress"></a>Pobierz oczekującego żądania — stan żądania to "w toku"

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Żądanie  
 POBIERZ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 LUB  

 POBIERZ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

> [!NOTE]
>  Jeśli *request_id* jest określona w zapytaniu, zachowuje się jak filtr. Jeśli *request_id* w zapytaniu i w obiekcie oczekujące są różne, zwracany jest kod stanu http 404.  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-complete"></a>Pobierz oczekującego żądania — stan żądania to "ukończone"

### <a name="request"></a>Żądanie  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 POBIERZ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 LUB  

 POBIERZ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "completed",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
}  

```  

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Pobierz oczekującego żądania - oczekujące żądania stanu "anulowania" lub "nie powiodło się"  

### <a name="request"></a>Żądanie  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 POBIERZ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 LUB  

 POBIERZ  `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "failed",  
  "status_details": "",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "error":  
    {  
        "code": "<errorcode>",    
        "message": "<message>"  
    }  
}  

```  

> [!NOTE]
> Wartość *errorcode* może być "Błąd wystawcy certyfikatu" lub "Odrzucone" na podstawie wystawcy lub użytkownika Błąd odpowiednio.  

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Pobierz oczekującego żądania - oczekujące żądania stanu jest "usunięty" lub "zastąpione"  
 Oczekujący obiekt można usunąć lub zastąpione przez operację tworzenia/importu, gdy jego stan nie jest "w toku."

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Żądanie  
 POBIERZ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 LUB  

 POBIERZ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 404, ReasonPhrase: 'Not Found'  
{  
  "error":  
    {  
         "code": "PendingCertificateNotFound",  
        "message": "…"  
    }  
}  

```  

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Utwórz (lub zaimportować) podczas oczekującego żądania istnieje — "w toku" jest w stanie
 Oczekujący obiekt ma cztery stany; "w toku", "anulowane", "nie powiodło się" lub "ukończona".

 Gdy stan oczekującego żądania jest "w toku", Utwórz (i zaimportuj) operacji zakończy się niepowodzeniem z kodem stanu http 409 (konflikt).  

 Aby rozwiązać konflikt:  

 - Certyfikat jest tworzony ręcznie, można ukończyć certyfikat KV wykonując scalania lub usunąć oczekujące obiektu.  

 - Jeśli certyfikat jest tworzony z wystawcą, można poczekać, aż do ukończenia certyfikat, nie powiedzie się lub została anulowana. Można też usunąć Oczekujący obiekt.

> [!NOTE]
> Usuwanie obiektu oczekujące może lub nie może anulować x509 żądania certyfikatu z dostawcą.  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>Żądanie  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert"  
    }  
  }  
}  

```  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 409, ReasonPhrase: 'Conflict'  
{  
  "error":  
    {  
        "code": "Forbidden",  
        "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."  
    }  
}  

```  

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Scalanie po utworzeniu oczekującego żądania z wystawcą
 Scalania nie jest dozwolona, gdy jest tworzony z wystawcą Oczekujący obiekt, ale jest dozwolone, gdy jego stan to "w toku." 

 Jeśli żądanie utworzenia x509 certyfikatu nie powiedzie się lub anuluje jakiegoś powodu i jeśli x509 certyfikat może zostać pobrany w sposób poza pasmem, można wykonać operacji scalania w celu przeprowadzenia KV certyfikatu.  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Żądanie  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

### <a name="response"></a>Odpowiedź  

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'  
{  
  "error":  
    {  
       "code": "Forbidden",  
       "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."  
    }  
}  

```  

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Żądania anulowania, gdy stan oczekującego żądania to "w toku"  
 Się żądać tylko wtedy anulowania.  Żądanie może lub nie można anulować. Jeśli żądanie nie jest "w toku", zwracany jest stan http 400 (nieprawidłowe żądanie).  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Żądanie  
 POPRAWKI `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 LUB  

 POPRAWKI `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

```json
{  
  "cancellation_requested": true  
}  

```  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": true,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="delete-a-pending-request-object"></a>Usuwanie obiektu oczekującego żądania  

> [!NOTE]
> Usunięcie obiektu oczekujące może lub nie może anulować x509 żądania certyfikatu z dostawcą.  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Żądanie  
 USUŃ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 LUB  

 USUŃ `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
}  
```  

## <a name="create-a-kv-certificate-manually"></a>Ręcznie Utwórz certyfikat KV  
 Można utworzyć certyfikatu z urzędu certyfikacji wybranych przez użytkownika przez proces ręcznego tworzenia. Nazwa wystawcy ustawioną wartość "Nieznane" lub nie określaj polu wystawcy.  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>Żądanie  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    }  
  "issuer": {  
       "name": "Unknown"  
    }  
  }  
}  

```  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "Unknown"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "status": "inProgress",  
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Scalanie po utworzeniu żądania oczekującego — utworzenie ręcznego certyfikatu  

|Metoda|Identyfikator URI żądania|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Żądanie  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

|Nazwa elementu|Wymagane|Typ|Wersja|Opis|  
|------------------|--------------|----------|-------------|-----------------|  
|x5c|Yes|tablica|\<wprowadzenie do wersji >|X509 łańcuch certyfikatów jako podstawowej tablicy ciągów 64.|  

### <a name="response"></a>Odpowiedź  

```  
StatusCode: 201, ReasonPhrase: 'Created'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
{  
"id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "cer": "……de34534……",  
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",  
    "attributes": {  
        "enabled": true,  
        "exp": 1530394215,  
        "nbf": 1435699215,  
        "created": 1435699919,  
        "updated": 1435699919  
    },  
    "pending": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"  
    },  
    "policy": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",  
        "key_props": {  
            "exportable": false,  
            "kty": "RSA",  
            "key_size": 2048,  
            "reuse_key": false  
        },  
        "secret_props": {  
            "contentType": "application/x-pkcs12"  
        },  
        "x509_props": {  
            "subject": "CN=Mycert1",  
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],  
                       "validity_months":12  
        },  
        "lifetime_actions": [{  
            "trigger": {  
                "lifetime_percentage": 80  
            },  
            "action": {  
                "action_type": "EmailContacts"  
            }  
        }],  
        "issuer": {  
            "name": "Unknown"  
        },  
        "attributes": {  
            "enabled": true,  
            "created": 1435699811,  
            "updated": 1435699811  
        }  
    }  
}  

```

## <a name="see-also"></a>Zobacz też
- [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
