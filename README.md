# GDPL
Generalized Domain Prompt Learning


# Training and Evaluation


#### (1) Base-to-Novel class generalization setting
The default training settings are provided in config file at `configs/trainers/MaPLe/vit_b16_c2_ep5_batch4_2ctx.yaml`. All hyper-parameters such as prompt length, prompt depth, etc., can be modified using this config file.

Below, we provide instructions to train MaPLe on imagenet. 


```bash

# seed=1
# trains and evaluates on base classes
bash scripts/maple/base2new_train_maple.sh imagenet 1
# evaluates on novel classes
bash scripts/maple/base2new_test_maple.sh imagenet 1

# seed=2
# trains and evaluates on base classes
bash scripts/maple/base2new_train_maple.sh imagenet 2
# evaluates on novel classes
bash scripts/maple/base2new_test_maple.sh imagenet 2

# seed=3
# trains and evaluates on base classes
bash scripts/maple/base2new_train_maple.sh imagenet 3
# evaluates on novel classes
bash scripts/maple/base2new_test_maple.sh imagenet 3
```

#### Averaging results over 3 seeds: 
Once the above trainings and evaluations are completed, the `output/` directory should have the following structure:

```
output
|–– base2new/
|   |–– test_new/
|   |   |–– imagenet/
|   |   |   |–– shots_16/
|   |   |   |   |–– MaPLe/
|   |   |   |   |   |–– vit_b16_c2_ep5_batch4_2ctx/
|   |   |   |   |   |   |–– seed1/
|   |   |   |   |   |   |–– seed2/
|   |   |   |   |   |   |–– seed3/
|   |–– train_base/
|   |   |–– imagenet/
|   |   |   |–– shots_16/
|   |   |   |   |–– MaPLe/
|   |   |   |   |   |–– vit_b16_c2_ep5_batch4_2ctx/
|   |   |   |   |   |   |–– seed1/
|   |   |   |   |   |   |–– seed2/
|   |   |   |   |   |   |–– seed3/
```

Now use the script `parse_test_res.py` and run the commands below to calculate the averaged results:
```bash
# prints averaged results for base classes
python parse_test_res.py output/base2new/train_base/imagenet/shots_16/MaPLe/vit_b16_c2_ep5_batch4_2ctx
# averaged results for novel classes
python parse_test_res.py output/base2new/test_new/imagenet/shots_16/MaPLe/vit_b16_c2_ep5_batch4_2ctx --test-log
```

The above steps can be repeated for other individual datasets.




#### (2) Cross-Dataset Transfer

We provide cross-dataset config : `configs/MaPLe/vit_b16_c2_ep5_batch4_2ctx_cross_datasets.yaml`.
* Firstly, train MaPLe on imagenet in few-shot manner (for all 3 seeds).

```bash
# seed=1 
bash scripts/maple/xd_train_maple.sh imagenet 1
# seed=2 
bash scripts/maple/xd_train_maple.sh imagenet 2
# seed=3 
bash scripts/maple/xd_train_maple.sh imagenet 3
```

* Now evaluate imageNet model on downstream datasets.

```bash
for SEED in 1 2 3
do
    bash scripts/maple/xd_test_maple.sh caltech101 ${SEED}
    bash scripts/maple/xd_test_maple.sh oxford_pets ${SEED}
    bash scripts/maple/xd_test_maple.sh stanford_cars ${SEED}
done
```

#### (3) Domain Generalization 
We use imagenet trained MaPLe model for domain generalization experiments. The steps are similar to above cross-dataset experiments, however, model is evaluated on imagenet variants.
* Evaluate imageNet model on variants of imagenet (domain shift datasets).

```bash
for SEED in 1 2 3
do
    bash scripts/maple/xd_test_maple.sh imagenetv2 ${SEED}
    bash scripts/maple/xd_test_maple.sh imagenet_sketch ${SEED}
    bash scripts/maple/xd_test_maple.sh imagenet_a ${SEED}
    bash scripts/maple/xd_test_maple.sh imagenet_r ${SEED}
done
```


You can obtain averaged results by using the script `parse_test_res.py` and following the similar steps as provided in base-to-novel generalization experiments.
<br>



