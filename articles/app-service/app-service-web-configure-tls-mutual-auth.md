---
title: Konfigurowanie wzajemnego uwierzytelniania TLS — Azure App Service
description: Dowiedz się, jak skonfigurować aplikację do używania uwierzytelniania certyfikatu klienta w protokole TLS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a07fa597305771ed3f4da01f2819297fc9cd3d77
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271690"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Konfigurowanie wzajemnego uwierzytelniania TLS dla Azure App Service

Możesz ograniczyć dostęp do aplikacji Azure App Service, włączając dla niej różne typy uwierzytelniania. Jednym ze sposobów jest zażądanie certyfikatu klienta, gdy żądanie klienta dotyczy protokołu TLS/SSL i zweryfikowania certyfikatu. Mechanizm ten jest nazywany uwierzytelnianiem obustronnym protokołu TLS lub uwierzytelnianiem certyfikatu klienta. W tym artykule pokazano, jak skonfigurować aplikację do korzystania z uwierzytelniania za pomocą certyfikatu klienta.

> [!NOTE]
> Jeśli uzyskujesz dostęp do witryny za pośrednictwem protokołu HTTP, a nie HTTPS, nie otrzymasz żadnych certyfikatów klienta. Dlatego jeśli aplikacja wymaga certyfikatów klienta, nie należy zezwalać na żądania do aplikacji za pośrednictwem protokołu HTTP.
>

## <a name="enable-client-certificates"></a>Włączanie certyfikatów klienta

Aby skonfigurować aplikację tak, aby wymagała certyfikatów klienta, należy ustawić ustawienie `clientCertEnabled` dla aplikacji na `true`. Aby ustawić ustawienie, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>Wyklucz ścieżki z wymagania uwierzytelniania

Po włączeniu uwierzytelniania wzajemnego dla aplikacji wszystkie ścieżki w katalogu głównym aplikacji będą wymagać certyfikatu klienta w celu uzyskania dostępu. Aby zapewnić, że pewne ścieżki pozostaną otwarte dla dostępu anonimowego, można zdefiniować ścieżki wykluczeń w ramach konfiguracji aplikacji.

Ścieżki wykluczeń można skonfigurować przez wybranie opcji **konfiguracja** > **Ustawienia ogólne** i zdefiniowanie ścieżki wykluczania. W tym przykładzie wszystkie elementy w ścieżce `/public` dla aplikacji nie zażądają certyfikatu klienta.

![Ścieżki wykluczania certyfikatów][exclusion-paths]


## <a name="access-client-certificate"></a>Dostęp do certyfikatu klienta

W App Service zakończenie żądania protokołu SSL w usłudze równoważenia obciążenia frontonu odbywa się. Podczas przekazywania żądania do kodu aplikacji z [włączonymi certyfikatami klienta](#enable-client-certificates)App Service wstrzyknąć nagłówek żądania `X-ARR-ClientCert` z certyfikatem klienta. App Service nie robi niczego z certyfikatem klienta innego niż przesłanie go do aplikacji. Kod aplikacji jest odpowiedzialny za Weryfikowanie certyfikatu klienta.

W przypadku ASP.NET certyfikat klienta jest dostępny za pomocą właściwości **HttpRequest. ClientCertificate** .

W przypadku innych stosów aplikacji (Node. js, PHP itp.) certyfikat klienta jest dostępny w aplikacji za pomocą zakodowanej wartości Base64 w nagłówku żądania `X-ARR-ClientCert`.

## <a name="aspnet-sample"></a>Przykład ASP.NET

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Przykład środowiska Node. js

Poniższy przykładowy kod w języku Node. js pobiera `X-ARR-ClientCert` nagłówku i używa [fałszowania węzła](https://github.com/digitalbazaar/forge) , aby przekonwertować ciąg PEM zakodowany algorytmem Base64 do obiektu certyfikatu i sprawdzić jego poprawność:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png