---
title: Jak skonfigurować wzajemne uwierzytelnianie protokołu TLS dla aplikacji sieci Web
description: Dowiedz się, jak skonfigurować aplikację sieci web, aby użyć uwierzytelniania certyfikatu klienta na TLS.
services: app-service
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836308"
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Jak skonfigurować wzajemne uwierzytelnianie protokołu TLS dla aplikacji sieci Web
## <a name="overview"></a>Omówienie
Przez włączenie różnych typów uwierzytelniania dla niego, można ograniczyć dostęp do aplikacji sieci web platformy Azure. Jednym ze sposobów jest uwierzytelnianie przy użyciu certyfikatu klienta, gdy żądanie zostanie protokołu TLS/SSL. Mechanizm ten nosi nazwę wzajemnego uwierzytelniania protokołu TLS lub certyfikatów klienta, uwierzytelniania i ten artykuł zawiera szczegółowe konfigurowanie aplikacji sieci web, aby użyć uwierzytelniania certyfikatu klienta.

> **Uwaga:** Jeśli uzyskujesz dostęp do witryny za pośrednictwem protokołu HTTP i HTTPS nie, nie otrzymasz żadnych certyfikatu klienta. Dlatego jeśli aplikacja wymaga certyfikatów klienta nie należy zezwalać żądań do aplikacji za pośrednictwem protokołu HTTP.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Konfigurowanie aplikacji sieci Web uwierzytelniania certyfikatu klienta
Można skonfigurować aplikację sieci web, aby wymagać certyfikaty klienta należy dodać ustawienie lokacji clientCertEnabled dla aplikacji sieci web i ustawioną wartość true. To ustawienie nie jest obecnie dostępna za pośrednictwem możliwości zarządzania w portalu, a musi być używane w tym celu interfejsu API REST.

Można użyć [narzędzie ARMClient](https://github.com/projectkudu/ARMClient) ułatwia spreparować wywołania interfejsu API REST. Po zalogowaniu się przy użyciu narzędzia należy wydać następujące polecenie:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

zastępowanie wszystko {} informacje dotyczące aplikacji sieci web i utworzeniu pliku o nazwie enableclientcert.json z następujących JSON zawartości:

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Upewnij się zmienić wartość "Lokalizacja" wszędzie tam, gdzie znajduje się aplikacja sieci web np. dla północno-środkowe stany lub zachodnie stany USA itp.

Można również użyć https://resources.azure.com do przerzucenia `clientCertEnabled` właściwości `true`.

> **Uwaga:** po uruchomieniu ARMClient z programu Powershell, należy wprowadzić symbol @ dla pliku JSON z tyłu znaczników ".
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Uzyskiwanie dostępu do certyfikatu klienta z aplikacji sieci Web
Jeśli używasz programu ASP.NET i skonfiguruj aplikację, aby użyć uwierzytelniania certyfikatu klienta, certyfikat będzie dostępna za pośrednictwem **HttpRequest.ClientCertificate** właściwości. Dla innych stosy aplikacji certyfikatu klienta będą dostępne w Twojej aplikacji za pomocą wartości kodowany w standardzie base64 w nagłówku żądania "X-ARR ClientCert". Aplikacji można utworzyć certyfikat z tej wartości, a następnie użyć go do celów uwierzytelniania i autoryzacji w aplikacji.

## <a name="special-considerations-for-certificate-validation"></a>Uwagi dotyczące weryfikacji certyfikatów
Certyfikat klienta, który jest wysyłany do aplikacji nie przechodzi przez wszystkie weryfikacji przez platformę Azure Web Apps. Sprawdzanie poprawności tego certyfikatu jest odpowiedzialny za aplikacji sieci web. Oto przykładowy kod platformy ASP.NET, która weryfikuje właściwości certyfikatu na potrzeby uwierzytelniania.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
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

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
