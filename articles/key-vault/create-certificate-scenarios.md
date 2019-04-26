---
title: Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
description: Scenariusze ukazujące szeroką gamę opcji tworzenia, monitorowania oraz interakcji z tworzenia certyfikatów przetwarzać za pomocą usługi Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 854d0e8f6927c9ce4855435a02b4819055111ceb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306023"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
Dotyczy: Azure

Poniżej 

Te scenariusze są operacje opisane w tym artykule:

- Żądanie certyfikatu KV z obsługiwanych wystawcą
- Pobierz oczekującego żądania — stan żądania to "w toku"
- Pobierz oczekującego żądania — stan żądania to "ukończony"
- Pobierz oczekującego żądania - oczekującego żądania stan to "anulowane" lub "nie powiodło się"
- Pobierz oczekującego żądania - oczekującego żądania stanu jest "usunięte" lub "zastąpione"
- Utwórz (lub importowanie) kiedy oczekujące żądanie istnieje — "w toku" jest w stanie
- Scalanie podczas oczekującego żądania jest tworzony przy użyciu wystawcy (na przykład DigiCert)
- Żądanie anulowania, gdy stan oczekującego żądania to "w toku"
- Usuń obiekt oczekującego żądania
- Ręczne tworzenie certyfikatu KV
- Scalanie po utworzeniu oczekującego żądania — Tworzenie ręcznego certyfikatu

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Żądanie certyfikatu KV z obsługiwanych wystawcą 

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Poniższe przykłady wymagają obiekt o nazwie "mydigicert" już były dostępne w magazynie kluczy przy użyciu dostawcy wystawcy jako firmy DigiCert. Wystawca certyfikatu jest reprezentowana w usłudze Azure klucza magazynu (KV) jako zasób CertificateIssuer jednostki. Umożliwia podawanie informacji o źródle certyfikatu KV; Nazwa wystawcy, dostawca, poświadczeń i inne szczegóły administracyjne.

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
> Jeśli *request_id* jest określona w zapytaniu, zachowuje się jak filtr. Jeśli *request_id* w zapytaniu i w obiekcie oczekujące są różne, zwracany jest kod stanu http 404.

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

## <a name="get-pending-request---request-status-is-complete"></a>Pobierz oczekującego żądania — stan żądania to "ukończony"

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Pobierz oczekującego żądania - oczekującego żądania stan to "anulowane" lub "nie powiodło się"

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
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> Wartość *errorcode* może być "Error wystawcy certyfikatu" lub "Odrzucone" na podstawie wystawcy lub użytkownika Błąd odpowiednio.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Pobierz oczekującego żądania - oczekującego żądania stanu jest "usunięte" lub "zastąpione"
Oczekiwanie obiektu można usunąć lub zastąpione przez operację tworzenia/importu, gdy jest on w stanie "w toku."

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
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Utwórz (lub importowanie) kiedy oczekujące żądanie istnieje — "w toku" jest w stanie
Oczekujące obiekt ma cztery stany; "w toku", "anulowane", "nie powiodło się" lub "ukończone".

Gdy stan oczekującego żądania to "w toku", Utwórz (i zaimportuj) operacji zakończy się niepowodzeniem z kodem stanu http 409 (konflikt).

Aby rozwiązać konflikt:

- Certyfikat jest tworzony ręcznie, można wykonać certyfikatu KV, wykonując scalanie lub Usuń oczekujące obiektu.

- Jeśli certyfikat jest tworzony z wystawcą, możesz poczekać, aż do ukończenia certyfikat, nie powiedzie się lub zostało anulowane. Alternatywnie możesz usunąć oczekujące obiektu.

> [!NOTE]
> Usuwanie oczekującego obiektu może lub nie może anulować x509 żądanie certyfikatu za pomocą dostawcy.

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
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Scalanie po utworzeniu żądania oczekującego żądania z wystawcą
Merge nie jest dozwolone, gdy oczekujące obiektu jest tworzona przy użyciu wystawcy, ale jest dozwolone, gdy jego stan to "w toku." 

Jeśli żądanie utworzenia x509 certyfikatu nie powiedzie się lub anuluje jakiegoś powodu, a jeśli x509 certyfikatów mogą być pobierane według out-of-band oznacza, można wykonać operacji scalania w celu przeprowadzenia certyfikatu KV.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Żądanie

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Odpowiedź

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Żądanie anulowania, gdy stan oczekującego żądania to "w toku"
Można jedynie żądać anulowania. Żądanie może lub nie można anulować. Jeśli żądanie nie jest "w toku", zwracany jest stan http 400 (złe żądanie).

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

## <a name="delete-a-pending-request-object"></a>Usuń obiekt oczekującego żądania

> [!NOTE]
> Usuwanie oczekującego obiektu może lub nie może anulować x509 żądanie certyfikatu za pomocą dostawcy.

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

## <a name="create-a-kv-certificate-manually"></a>Ręczne tworzenie certyfikatu KV
Można utworzyć certyfikat z urzędu certyfikacji w wybranym przez proces ręcznego tworzenia. Ustaw nazwę wystawcy "Nieznane" lub nie określaj pole wystawcy.

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Scalanie po utworzeniu oczekującego żądania — Tworzenie ręcznego certyfikatu

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Żądanie

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Nazwa elementu|Wymagane|Type|Wersja|Opis|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Yes|tablica|\<Przedstawiamy wersję >|Łańcuch certyfikatów w X509 jako podstawowy tablicy ciągów 64.|

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
            "validity_months": 12
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
