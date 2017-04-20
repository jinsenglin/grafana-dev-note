# Goal: add custom data table and data HTTP API

Files

* M pkg/api/api.go
  * uses pkg/api/custom_data.go
  * uses pkg/api/dtos/custom_data.go
  
* A pkg/api/custom_data.go
  * uses pkg/api/dtos/custom_data.go
  * uses pkg/models/custom_data.go
  
* A pkg/api/dtos/custom_data.go
* A pkg/models/custom_data.go
* A pkg/services/sqlstore/custom_data.go
  * uses pkg/models/custom_data.go

* M pkg/services/sqlstore/migrations/migrations.go
  * uses pkg/services/sqlstore/migrations/custom\_data_mig.go 

* A pkg/services/sqlstore/migrations/custom\_data_mig.go

---
