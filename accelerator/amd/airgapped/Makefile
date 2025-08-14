# Add support for import variables from an external file to eliminate the need to
# define them on the commandline OR support multiple invocations of the makefile
# targets across different package versions and architecture
ENV_MAKEFILE ?= env.mk
-include $(ENV_MAKEFILE)

CONTAINER_MANAGER ?= podman 

BASE_IMAGE_EXTRACTOR ?= quay.io/centos/centos:stream9
BASE_IMAGE ?= quay.io/centos/centos:stream9-minimal
REPO_BASE_URL ?= https://repo.radeon.com
OS_DISTRO ?= el
OS_VERSION_ID ?= 9.6
ROCM_VERSION ?=
AMD_DRIVER_VERSION ?=

OCP_VERSION ?= v4.19
KERNEL_VERSION ?= 5.14.0-570.27.1.el9_6.x86_64
DRIVER_TOOLKIT_IMAGE ?= registry.redhat.io/openshift4/driver-toolkit-rhel9:$(OCP_VERSION)

IMAGE_REGISTRY_ORG ?= "<<quay.io/IMAGE-ORG>>"
IMAGE_REGISTRY_NAMESPACE ?= "<<IMAGE REPOSITORY NAME>>"
IMAGE_REGISTRY_NAMESPACE_AMDGPU_SRC ?= "<<AMDGPU IMAGE REPOSITORY NAME>>"
IMAGE_REGISTRY_NAMESPACE_KMM_BUILD ?= "<<KMM IMAGE REPOSITORY NAME>>"
AMDGPU_DKMS_SRC_IMAGE ?= $(IMAGE_REGISTRY_ORG)/$(IMAGE_REGISTRY_NAMESPACE_AMDGPU_SRC):rocm$(ROCM_VERSION)-$(OS_DISTRO)$(OS_VERSION_ID)
AMDGPU_KMM_IMAGE ?= $(IMAGE_REGISTRY_ORG)/$(IMAGE_REGISTRY_NAMESPACE_KMM_BUILD):rocm$(ROCM_VERSION)-$(OS_DISTRO)$(OS_VERSION_ID)

CONTAINERFILE_EXTRACTOR ?= Containerfile.amdgpu-dkms-extractor
CONTAINERFILE_KMM_BUILD ?= Containerfile.amdgpu-dkms-kmm

.PHONY: image-extractor image-kmm-build

image-extractor:
	$(CONTAINER_MANAGER) build -f $(CONTAINERFILE_EXTRACTOR) -t $(AMDGPU_DKMS_SRC_IMAGE) \
		--build-arg BASE_IMAGE_EXTRACTOR=$(BASE_IMAGE_EXTRACTOR) \
		--build-arg BASE_IMAGE=$(BASE_IMAGE) \
		--build-arg ROCM_VERSION=$(ROCM_VERSION) \
		--build-arg AMD_DRIVER_VERSION=$(AMD_DRIVER_VERSION) \
		--build-arg OS_DISTRO=$(OS_DISTRO) \
		--build-arg OS_VERSION_ID=$(OS_VERSION_ID)

image-kmm-build:
	$(CONTAINER_MANAGER) build -f $(CONTAINERFILE_KMM_BUILD) -t $(AMDGPU_KMM_IMAGE) \
		--build-arg AMDGPU_DKMS_SRC_IMAGE=$(AMDGPU_DKMS_SRC_IMAGE) \
		--build-arg BASE_IMAGE=$(BASE_IMAGE) \
		--build-arg DTK_AUTO=$(DRIVER_TOOLKIT_IMAGE) \
		--build-arg KERNEL_VERSION=$(KERNEL_VERSION) \
		--build-arg ROCM_VERSION=$(ROCM_VERSION) \
		--build-arg AMD_DRIVER_VERSION=$(AMD_DRIVER_VERSION)
