# Hugging Face Inference SageMaker Module

Terraform module for easy deployment of a [Hugging Face Transformer models](hf.co/models) to [Amazon SageMaker](https://aws.amazon.com/de/sagemaker/) real-time endpoints. This module will create all the necessary resources to deploy a model to Amazon SageMaker including IAM roles, if not provided, SageMaker Model, SageMaker Endpoint Configuration, SageMaker endpoint.

With this module you can deploy [Hugging Face Transformer](hf.co/models) directly from the [Model Hub](hf.co/models) or from Amazon S3 to Amazon SageMaker for PyTorch and Tensorflow based models.

## Usage

```hcl
module "sagemaker-huggingface" {
  source                   = "philschmid/sagemaker-huggingface/aws"
  version                  = "0.2.0"
  name_prefix              = "distilbert"
  pytorch_version          = "1.9.1"
  transformers_version     = "4.12.3"
  instance_type            = "ml.g4dn.xlarge"
  instance_count           = 1 # default is 1
  hf_model_id              = "distilbert-base-uncased-finetuned-sst-2-english"
  hf_task                  = "text-classification"
}
```

**examples:**
* [Deploy Model from hf.co/models](./examples/deploy_from_hub/main.tf)
* [Deploy Model from Amazon S3](./examples/deploy_from_s3/main.tf)
* [Tensorflow example](./examples/tensorflow_example/main.tf)
* [Deploy Model with existing IAM role](./examples/use_existing_iam_role/main.tf)

## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 1.0.0 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | 3.74.0 |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [aws_iam_role.new_role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_sagemaker_endpoint.huggingface](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sagemaker_endpoint) | resource |
| [aws_sagemaker_endpoint_configuration.huggingface](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sagemaker_endpoint_configuration) | resource |
| [aws_sagemaker_model.model_with_hub_model](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sagemaker_model) | resource |
| [aws_sagemaker_model.model_with_model_artifact](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sagemaker_model) | resource |
| [aws_iam_role.get_role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_role) | data source |
| [aws_sagemaker_prebuilt_ecr_image.deploy_image](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/sagemaker_prebuilt_ecr_image) | data source |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_hf_model_id"></a> [hf\_model\_id](#input\_hf\_model\_id) | The HF\_MODEL\_ID environment variable defines the model id, which will be automatically loaded from [hf.co/models](https://huggingface.co/models) when creating or SageMaker Endpoint. | `string` | `null` | no |
| <a name="input_hf_task"></a> [hf\_task](#input\_hf\_task) | The HF\_TASK environment variable defines the task for the used 🤗 Transformers pipeline. A full list of tasks can be find [here](https://huggingface.co/transformers/main_classes/pipelines.html). | `string` | n/a | yes |
| <a name="input_image_tag"></a> [image\_tag](#input\_image\_tag) | The image tag you want to use for the container you want to use. Defaults to `None`. The module tries to derive the `image_tag` from the `pytorch_version`, `tensorflow_version` & `instance_type`. If you want to override this, you can provide the `image_tag` as a variable. | `string` | `null` | no |
| <a name="input_instance_count"></a> [instance\_count](#input\_instance\_count) | The initial number of instances to run in the Endpoint created from this Model. Defaults to 1. | `number` | `1` | no |
| <a name="input_instance_type"></a> [instance\_type](#input\_instance\_type) | The EC2 instance type to deploy this Model to. For example, `ml.p2.xlarge`. | `string` | `null` | no |
| <a name="input_model_data"></a> [model\_data](#input\_model\_data) | The S3 location of a SageMaker model data .tar.gz file (default: None). Not needed when using `hf_model_id`. | `string` | `null` | no |
| <a name="input_name_prefix"></a> [name\_prefix](#input\_name\_prefix) | A prefix used for naming resources. | `string` | n/a | yes |
| <a name="input_pytorch_version"></a> [pytorch\_version](#input\_pytorch\_version) | PyTorch version you want to use for executing your inference code. Defaults to `None`. Required unless `tensorflow_version` is provided. [List of supported versions](https://huggingface.co/docs/sagemaker/reference#inference-dlc-overview) | `string` | `null` | no |
| <a name="input_sagemaker_execution_role"></a> [sagemaker\_execution\_role](#input\_sagemaker\_execution\_role) | An AWS IAM role Name to access training data and model artifacts. After the endpoint is created, the inference code might use the IAM role if it needs to access some AWS resources. If not specified, the role will created with with the `CreateModel` permissions from the [documentation](https://docs.aws.amazon.com/sagemaker/latest/dg/sagemaker-roles.html#sagemaker-roles-createmodel-perms) | `string` | `null` | no |
| <a name="input_tags"></a> [tags](#input\_tags) | A map of tags (key-value pairs) passed to resources. | `map(string)` | `{}` | no |
| <a name="input_tensorflow_version"></a> [tensorflow\_version](#input\_tensorflow\_version) | TensorFlow version you want to use for executing your inference code. Defaults to `None`. Required unless `pytorch_version` is provided. [List of supported versions](https://huggingface.co/docs/sagemaker/reference#inference-dlc-overview) | `string` | `null` | no |
| <a name="input_transformers_version"></a> [transformers\_version](#input\_transformers\_version) | Transformers version you want to use for executing your model training code. Defaults to None. [List of supported versions](https://huggingface.co/docs/sagemaker/reference#inference-dlc-overview) | `string` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_iam_role"></a> [iam\_role](#output\_iam\_role) | IAM role used in the endpoint |
| <a name="output_sagemaker_endpoint"></a> [sagemaker\_endpoint](#output\_sagemaker\_endpoint) | created Amazon SageMaker endpoint resource |
| <a name="output_sagemaker_endpoint_configuration"></a> [sagemaker\_endpoint\_configuration](#output\_sagemaker\_endpoint\_configuration) | created Amazon SageMaker endpoint configuration resource |
| <a name="output_sagemaker_endpoint_name"></a> [sagemaker\_endpoint\_name](#output\_sagemaker\_endpoint\_name) | Name of the created Amazon SageMaker endpoint, used for invoking the endpoint, with sdks |
| <a name="output_sagemaker_model"></a> [sagemaker\_model](#output\_sagemaker\_model) | created Amazon SageMaker model resource |
| <a name="output_tags"></a> [tags](#output\_tags) | n/a |
| <a name="output_used_container"></a> [used\_container](#output\_used\_container) | Used container for creating the endpoint |


## License

MIT License. See [LICENSE](LICENSE) for full details.


