## Training and Testing Environment

- **Framework**：LLama_factory
- **Dataset**：100k training data, 1k testing data
- **Training Environment**：8 * NVIDIA A100-SXM4-80GB

### Training Time

- **Lora**: 约 8 小时
- **PlusLora**: 约 8 小时
- **RsLora**: 约 8 小时
- **Dora**: 约 18 小时


## Example Training Commands

```bash
CUDA_VISIBLE_DEVICES=0 nohup python src/train_bash.py \
--stage sft \
--do_train True \
--model_name_or_path /data/khazic/yi9 \
--finetuning_type lora \
--template default \
--dataset_dir data \
--dataset adgen_train \
--cutoff_len 1024 \
--learning_rate 1e-05 \
--num_train_epochs 3.0 \
--max_samples 100000 \
--per_device_train_batch_size 32 \
--gradient_accumulation_steps 4 \
--lr_scheduler_type cosine \
--max_grad_norm 1.0 \
--logging_steps 5 \
--save_steps 100000 \
--warmup_steps 100 \
--optim adamw_torch \
--output_dir /data/khazic/test_deo/Lora \
--bf16 True \
--lora_rank 64 \
--lora_alpha 64 \
--lora_dropout 0.1 \
--lora_target all \
--plot_loss True \
--load_best_model_at_end False \
--ddp_timeout 180000000 > /data/khazic/logs/task1.log 2>&1 &
```

## Example Testing Commands

```bash
CUDA_VISIBLE_DEVICES=4 python /data/khazic/llama/src/train_bash.py \
--stage sft \
--do_predict \
--model_name_or_path /data/khazic/yi9 \
--adapter_name_or_path /data/khazic/test_deo/Lora \
--dataset adgen_eval \
--dataset_dir /data/khazic/llama/data \
--template default \
--finetuning_type lora \
--output_dir /data/khazic/result/Lora \
--overwrite_cache \
--overwrite_output_dir \
--cutoff_len 1024 \
--preprocessing_num_workers 16 \
--per_device_eval_batch_size 16 \
--max_samples 100000 \
--predict_with_generate \
--bf16
```

### Test_Rouge Results

| Model    | predict_bleu-4       | predict_rouge-1     | predict_rouge-2     | predict_rouge-l     | predict_runtime | predict_samples_per_second | predict_steps_per_second |
|----------|----------------------|---------------------|---------------------|---------------------|-----------------|----------------------------|---------------------------|
| Lora     | 7.855615233644861    | 31.724911588785044  | 7.053533644859813   | 25.207992990654205  | 385.2302        | 2.778                      | 0.174                     |
| PlusLora | 8.355670280373833    | 32.02332392523365   | 7.518382897196262   | 25.703628785046728  | 377.8093        | 2.832                      | 0.177                     |
| RsLora   | 8.026634953271028    | 31.83490514018691   | 7.261890654205607   | 25.5884861682243    | 375.99          | 2.846                      | 0.178                     |
| Dora     | 7.9877148598130825   | 31.695666168224303  | 7.211546822429907   | 25.101086542056073  | 378.7636        | 2.825                      | 0.177                     |
