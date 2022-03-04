# Best practices for deploying models on SageMaker Hosting Services<a name="deployment-best-practices"></a>

When hosting models using SageMaker hosting services, consider the following:
+ Typically, a client application sends requests to the SageMaker HTTPS endpoint to obtain inferences from a deployed model\. You can also send requests to this endpoint from your Jupyter notebook during testing\.
+ You can deploy a model trained with SageMaker to your own deployment target\. To do that, you need to know the algorithm\-specific format of the model artifacts that were generated by model training\. For more information about output formats, see the section corresponding to the algorithm you are using in [Common Data Formats for Training](cdf-training.md)\. 
+ You can deploy multiple variants of a model to the same SageMaker HTTPS endpoint\. This is useful for testing variations of a model in production\. For example, suppose that you've deployed a model into production\. You want to test a variation of the model by directing a small amount of traffic, say 5%, to the new model\. To do this, create an endpoint configuration that describes both variants of the model\. You specify the `ProductionVariant` in your request to the `CreateEndPointConfig`\. For more information, see [https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_ProductionVariant.html](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_ProductionVariant.html)\. 
+ You can configure a `ProductionVariant` to use Application Auto Scaling\. For information about configuring automatic scaling, see [Automatically Scale Amazon SageMaker Models](endpoint-auto-scaling.md)\.
+ You can modify an endpoint without taking models that are already deployed into production out of service\. For example, you can add new model variants, update the ML Compute instance configurations of existing model variants, or change the distribution of traffic among model variants\. To modify an endpoint, you provide a new endpoint configuration\. SageMaker implements the changes without any downtime\. For more information see, [https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_UpdateEndpoint.html](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_UpdateEndpoint.html) and [https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_UpdateEndpointWeightsAndCapacities.html](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_UpdateEndpointWeightsAndCapacities.html)\. 
+ Changing or deleting model artifacts or changing inference code after deploying a model produces unpredictable results\. If you need to change or delete model artifacts or change inference code, modify the endpoint by providing a new endpoint configuration\. Once you provide the new endpoint configuration, you can change or delete the model artifacts corresponding to the old endpoint configuration\.
+ If you want to get inferences on entire datasets, consider using batch transform as an alternative to hosting services\. For information, see [Use Batch Transform](batch-transform.md) 

## Deploy Multiple Instances Across Availability Zones<a name="deployment-best-practices-availability-zones"></a>

**Create robust endpoints when hosting your model\.** SageMaker endpoints can help protect your application from [Availability Zone](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) outages and instance failures\. If an outage occurs or an instance fails, SageMaker automatically attempts to distribute your instances across Availability Zones\. For this reason, we strongly recommended that you deploy multiple instances for each production endpoint\. 

If you are using an [Amazon Virtual Private Cloud \(VPC\)](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html), configure the VPC with at least two [https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_VpcConfig.html#SageMaker-Type-VpcConfig-Subnets                     .html](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_VpcConfig.html#SageMaker-Type-VpcConfig-Subnets                     .html), each in a different Availability Zone\. If an outage occurs or an instance fails, Amazon SageMaker automatically attempts to distribute your instances across Availability Zones\. 

In general, to achieve more reliable performance, use more small [Instance Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in different Availability Zones to host your endpoints\.