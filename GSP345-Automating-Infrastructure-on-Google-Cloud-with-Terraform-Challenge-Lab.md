# GSP345-Automating-Infrastructure-on-Google-Cloud-with-Terraform-Challenge-Lab

## Prerequisite :

##### 1) Run Following Code in Cloud Shell

```
touch main.tf
touch variables.tf
mkdir modules
cd modules
mkdir instances
cd instances
touch instances.tf
touch outputs.tf
touch variables.tf
cd ..
mkdir storage
cd storage
touch storage.tf
touch outputs.tf
touch
```


##### 2) Add the following to the each _variables.tf_ file, and fill in the _GCP Project ID_:
```
variable "region" {
 default = "<Enter region which in given to you>"
}

variable "zone" {
 default = "<Enter zone which in given to you>"
}

variable "project_id" {
 default = "<Enter Project Id>"
}
```

##### 3) Add the following to the _main.tf_ file:
```
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "3.55.0"
    }
  }
}

provider "google" {
  project     = var.project_id
  region      = var.region
  zone        = var.zone
}


module "instances" {
  source     = "./modules/instances"
}
```


##### 4) Run "terraform init" in Cloud Shell in the root directory to initialize terraform.
```
terraform init
```



## TASK 1: Import infrastructure

##### 1) Navigate to Compute Engine > VM Instances. Click on tf-instance-1. Copy the Instance ID down somewhere to use later.
##### Navigate to Compute Engine > VM Instances. Click on tf-instance-2. Copy the Instance ID down somewhere to use later.
##### Next, navigate to modules/instances/instances.tf. Copy the following configuration into the file:
```
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "n1-standard-1"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

metadata_startup_script = <<-EOT
#!/bin/bash
EOT

allow_stopping_for_update = true
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "n1-standard-1"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

metadata_startup_script = <<-EOT
#!/bin/bash
EOT

allow_stopping_for_update = true
}
```


##### 2) To import the first instance, use the following command, using the Instance ID for tf-instance-1 you copied down earlier.
```
terraform import module.instances.google_compute_instance.tf-instance-1 <FILL IN INSTANCE 1 ID>
```


##### 3) To import the second instance, use the following command, using the Instance ID for tf-instance-2 you copied down earlier.
```
terraform import module.instances.google_compute_instance.tf-instance-2 <FILL IN INSTANCE 2 ID>
```


##### 4)The two instances have now been imported into your terraform configuration. You can now run the commands to update the state of Terraform. Type yes at the dialogue after you run the apply command to accept the state changes.
```
terraform plan
terraform apply
```




## TASK 2: Configure a remote backend

##### 1) Add the following code to the modules/storage/storage.tf file, and fill in the Bucket Name:

```
resource "google_storage_bucket" "storage-bucket" {
  name          = "<ENTER BUCKET NAME>"
  location      = "US"
  force_destroy = true
  uniform_bucket_level_access = true
}
```

##### 2) Next, add the following to the main.tf file:

```
module "storage" {
  source     = "./modules/storage"
}
```


##### 3) Run the following commands to initialize the module and create the storage bucket resource. Type yes at the dialogue after you run the apply command to accept the state changes.

```
terraform init
terraform apply
```

##### 4) Next, update the main.tf file so that the terraform block looks like the following. Fill in your GCP Project ID for the bucket argument definition.

```
terraform {
  backend "gcs" {
    bucket  = "<ENTER BUCKET NAME>"
 prefix  = "terraform/state"
  }
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "3.55.0"
    }
  }
}
```

##### 5) Run the following to initialize the remote backend. Type yes at the prompt.

```
terraform init
```


## TASK 3: Modify and update infrastructure

##### 1) Navigate to modules/instances/instance.tf. Replace the entire contents of the file with the following, and fill in your Instance 3 ID:

```
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "n1-standard-2"
  zone         = "<Enter zone which in given to you>"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

  metadata_startup_script = <<-EOT
#!/bin/bash
EOT
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "n1-standard-2"
  zone         = "<Enter zone which in given to you>"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

  metadata_startup_script = <<-EOT
#!/bin/bash
EOT
}

resource "google_compute_instance" "<ENTER INSTANCE 3 NAME>" {
  name         = "<ENTER INSTANCE 3 NAME>"
  machine_type = "n1-standard-2"
  zone         = "<Enter zone which in given to you>"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

  metadata_startup_script = <<-EOT
#!/bin/bash
EOT
}
```


##### 2) Run the following commands to initialize the module and create/update the instance resources. Type yes at the dialogue after you run the apply command to accept the state changes.

```
terraform init
terraform apply
```


## TASK 4: Taint and destroy resources

##### 1) Taint the tf-instance-3 resource by running the following command, and fill in your Instance 3 ID:

```
terraform taint module.instances.google_compute_instance.<ENTER INSTANCE 3 NAME>
```

##### 2) Run the following commands to apply the changes:

```
terraform init
terraform apply
```


##### 3) Remove the tf-instance-3 resource from the instances.tf file. Delete the following code chunk from the file.

```
resource "google_compute_instance" "<ENTER INSTANCE 3 NAME>" {
  name         = "<ENTER INSTANCE 3 NAME>"
  machine_type = "n1-standard-2"
  zone         = "<Enter zone which in given to you>"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

  metadata_startup_script = <<-EOT
#!/bin/bash
EOT
}
```


##### 4) Run the following commands to apply the changes. Type yes at the prompt.

```
terraform init
terraform apply
```





## TASK 5: Use a module from the Registry


##### 1) Copy and paste the following to the end of main.tf file, fill in Version Number and Network Name instructed in the challenge:

```
module "vpc" {
    source  = "terraform-google-modules/network/google"
    version = "~> <ENTER VERSION NUMBER>"

    project_id   = "<ENTER PROJECT ID>"
    network_name = "<ENTER VPC Name>"
    routing_mode = "GLOBAL"

    subnets = [
        {
            subnet_name           = "subnet-01"
            subnet_ip             = "10.10.10.0/24"
            subnet_region         = "<ENTER REGION WHICH IS GIVEN TO YOU>"
        },
        {
            subnet_name           = "subnet-02"
            subnet_ip             = "10.10.20.0/24"
            subnet_region         = "<ENTER REGION WHICH IS GIVEN TO YOU>"
            subnet_private_access = "true"
            subnet_flow_logs      = "true"
            description           = "This subnet has a description"
        }
    ]
}
```



##### 2) Run the following commands to initialize the module and create the VPC. Type yes at the prompt.
```
terraform init
terraform apply
```



##### 3) Navigate to modules/instances/instances.tf. Replace the entire contents of the file with the following:
```
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "n1-standard-2"
  zone         = "<ENTER REGION WHICH IS GIVEN TO YOU>"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "<ENTER VPC Name>"
    subnetwork = "subnet-01"
  }

metadata_startup_script = <<-EOT
        #!/bin/bash
    EOT

}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "n1-standard-2"
  zone         = "<ENTER REGION WHICH IS GIVEN TO YOU>"
  allow_stopping_for_update = true

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "<ENTER VPC Name>"
    subnetwork = "subnet-02"
  }

metadata_startup_script = <<-EOT
        #!/bin/bash
    EOT

}
```

##### 4) Run the following commands to initialize the module and update the instances. Type yes at the prompt.

```
terraform init
terraform apply
```



## TASK 6: Configure a firewall

##### 1) Add the following resource to the main.tf file, fill in the GCP Project ID and Network Name:

```
resource "google_compute_firewall" "tf-firewall" {
  name    = "tf-firewall"
 network = "projects/<FILL IN PROJECT_ID>/global/networks/<ENTER VPC Name>"

  allow {
    protocol = "tcp"
    ports    = ["80"]
  }

  source_tags = ["web"]
  source_ranges = ["0.0.0.0/0"]
}
```


##### 2) Run the following commands to configure the firewall. Type yes at the prompt.
```
terraform init
terraform apply
```





