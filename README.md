# STUDY-ImageMigration
Study notes for Image Converter Migration from EC2 to GAE.

## A. GAE Quickstart
1. Install: Java SDK / Google Cloud SDK / Git / Appache Maven
1. HelloWorld project deploying (maven local & gae remote)

> **Reference**  
> - [Quickstart for Java 8 for App Engine Standard Environment](https://cloud.google.com/appengine/docs/standard/java/quickstart)  
> - [HelloWorld | GAE](https://gae-hello-world-230206.appspot.com/)

## B. imagec project deploy
1. Include 3rd-party dependencies in Maven (B-1)
1. Build success but run time error: Language file reading

> **Reference**  
> - [Setting Up Your Development Environment](https://cloud.google.com/appengine/docs/standard/java/building-app/environment-setup)  
> - [3 ways to add local jar to maven project](http://roufid.com/3-ways-to-add-local-jar-to-maven-project/)

### B-1. 3rd-party libraries in Maven
1. 指定第三方程式庫來源並設定 pom.xml 檔來利用 Maven 自動下載，而非自行引入 jar 檔
1. 無 central 來源的程式庫似乎無法下載
1. 1.) 的方法僅適用於 compile 階段，如欲在 runtime 正確引入程式庫，則需另開 repository 來安裝。

> **Reference**  
> - [How To Download Jars From Maven Repository](https://www.dev2qa.com/how-to-download-jars-from-maven-repository/)  
> - [MVN Respository](https://mvnrepository.com/)  
> - [Package image4j Not Found in Runtime | GitHub Issues](https://github.com/nizniz187/imagec-gae/issues/7)

## C. Google Cloud Storage
1. 描述直接透過 web console GUI 以及 gsutil command tool 來存取檔案的方式。
2. Client Library 指的是使用各大程式語言實作與 GCP 溝通的程式庫。

> **Reference**  
> - [Google Cloud Storage](https://cloud.google.com/storage/)

## D. Storing Data with GCP Storage (Java)
1. 運用 GAE Cloudstorage API 完成圖檔寫入。
2. com.google.cloud.storage 和 com.google.appengine.tools.cloudstorage 不同。後者只適用於 appengine 上的應用程式，且 Java 支援版本較舊。**應改採用前者**。(D-1)
3. Storage Free 5GB 儲存只限預設 bucket。若欲作為各 APP 共用，則需要**階層式儲存檔案**（資料夾）。(D-2)

> **Reference**  
> - [Google Cloud Storage](https://cloud.google.com/storage/)

### D-1. GCS Java API | TK
1. 運用 GCS Java API 完成圖檔寫入、讀取與刪除。
2. 若應用程式為在 GAE 標準環境上執行，則使用 GCS 服務時須先**取得憑證**。(D-1-1)
3. 重新理解 GAE / GCS 作為服務在 GCP 下的架構。(D-1-2)
4. Storage Free 5GB 限制使用 Regional 的特定區域。但使用 gcloud 工具建立的 gae app 會自動建立 bucket 並設定為 multi-regional。這是否意味著：A. GAE App 儲存的檔案不包含在免費方案中，或者 B. 需另開 regional bucket 並設定權限予該 GAE App 存取使用？(D-1-3)

> **Reference**  
> - [Google Cloud Storage JAVA API](https://googleapis.github.io/google-cloud-java/google-cloud-clients/apidocs/index.html)  
> - [Using Cloud Storage with Java for the App Engine standard environment](https://cloud.google.com/java/getting-started-appengine-standard/using-cloud-storage/)

#### D-1-1. Setting Service Account Credentials
1. 直接在程式中使用 AppEngineCredentials 物件來取得預設憑證。
2. 在 GCP Console 中，啟用 Google App Engine Admin API。
3. Runtime Error - token has been expired: cmd "gcloud auth application-default login"
4. Runtime Error - Unknown project id: StorageOptions.Builder.setProjectId("PROJECT_ID")

> **Reference**  
> - [在 App Engine 標準環境中取得憑證 | 設定伺服器對伺服器生產應用程式的驗證作業](https://cloud.google.com/docs/authentication/production?hl=zh-tw#obtaining_and_providing_service_account_credentials_manually#obtaining_credentials_on_app_engine_standard_environment)  
> - [Using Your Service Account | Access Control](https://cloud.google.com/appengine/docs/standard/java/access-control)  
> - [How to set project ID in google cloud storage builder?](https://stackoverflow.com/questions/47388452/how-to-set-project-id-in-google-cloud-storage-builder)

#### D-1-2. GCP from a Higher Scope
1. 了解 GCP 資源的地理位置分配概念。(D-1-2-1)
2. Project 概念並非只能以一個網站應用程式為單位。例如：亦可使用一個專案集中管理其他專案所使用的 GCP Buckets。

> **Reference**  
> - [GCP Overview](https://cloud.google.com/docs/overview/)  
> - [GCP Projects](https://cloud.google.com/storage/docs/projects)

#### D-1-2-1. GCP Geography & Regions
1. 地理位置的層級選擇，主要考量為存取速度與可用性（備援）。
2. GAE App 屬 Regional，但 GAE 自動創建的所屬 Bucket 為 Multi-Reginal。

> **Reference**  
> - [Geography & Regions](https://cloud.google.com/docs/geography-and-regions#geographic_management_of_data)  
> - [Bucket Locations](https://cloud.google.com/storage/docs/locations)  
> - [GAE Locations](https://cloud.google.com/appengine/docs/locations)

#### D-1-3. GCS Free Amount
1. GAE 附帶的 GCS 服務似乎與獨立的 GCS 服務不同，兩者的免費額度皆為 5GB，分開計算。
2. GAE 附帶的 GCS 服務應為其所自動建立的所屬 Bucket。獨立的 GCS 服務有其特殊的免費額度限制。
3. 免費額度皆為 by account，而非 by project。

> **Reference**  
> - [GCP 免費版](https://cloud.google.com/free/docs/gcp-free-tier?hl=zh-tw)

### D-2. GCS Subdirectory | TK

> **Reference**  
> - [Bucket and Object Naming Guidelines](https://cloud.google.com/storage/docs/naming)

## E. GAE Overview | TK

> **Reference**  
> - [An Overview of App Engine](https://cloud.google.com/appengine/docs/standard/java/an-overview-of-app-engine)

### E-1. GAE Instance Management | TK
1. 如何 upgrade instance class

> **Reference**  
> - [How Instances are Managed](https://cloud.google.com/appengine/docs/standard/java/how-instances-are-managed)  
> - [Instance Classes | About the Standard Environment](https://cloud.google.com/appengine/docs/standard/#instance_classes)  
> - [Standard Instance Pricing | App Engine Pricing](https://cloud.google.com/appengine/pricing#standard_instance_pricing)

### E-2. Standard v.s. Flexible Environment | TK

### E-3. GAE Log | TK

### E-4. GAE Version | TK

## F. GCS Domain Name Verification | TK
1. 限制物件的網域存取權限

> **Reference**  
> - [以網域命名的值區驗證](https://cloud.google.com/storage/docs/domain-name-verification)

## G. GCP Authentication | TK

> **Reference**  
> [GCP Authentication](https://cloud.google.com/docs/authentication/)
