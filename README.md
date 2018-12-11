# kafkactl

command-line interface for interaction with kafka

## features
- auto-completion
- configuration of different contexts

## installation

### from source

```bash
go get -u github.com/deviceinsight/kafkactl
```

**NOTE:** make sure that `kafkactl` is on PATH otherwise auto-completion won't work.

## configuration

1. create `~/.kafkactl.yml` with a definition of contexts that should be available 

```yaml
contexts:
  localhost:
    brokers:
    - localhost:9092
  remote-cluster:
    brokers:
    - remote-cluster001:9092
    - remote-cluster002:9092
    - remote-cluster003:9092

current-context: localhost
```

2. in order to get auto completion add it in startup script of the shell:

- for `bash` add the following to `~/.bashrc`:
```bash
# kafkactl autocomplete
source <(kafkactl completion bash)
```

- for `zsh` add the following to `~/.zshrc`:
```bash
# kafkactl autocomplete
source <(kafkactl completion zsh)
```

- `fish` is currently not supported. see: https://github.com/spf13/cobra/issues/350

## examples

### producing messages

Producing messages can be done in multiple ways. If we want to produce a message with `key='my-key'`,
`value='my-value'` to the topic `my-topic` this can be achieved with one of the following commands:

```bash
echo "my-key#my-value" | kafkactl produce my-topic --separator=#
echo "my-value" | kafkactl produce my-topic --key=my-key
kafkactl produce my-topic --key=my-key --value=my-value
```

It is also possible to specify the partition to insert the message:
```bash
kafkactl produce my-topic --key=my-key --value=my-value --partition=2
```

Additionally a different partitioning scheme can be used. when a `key` is provided the default partitioner
uses the `hash` of the `key` to assign a partition. so the same `key` will end up in the same partition: 
```bash
# the following 3 messages will all be inserted to the same partition
kafkactl produce my-topic --key=my-key --value=my-value
kafkactl produce my-topic --key=my-key --value=my-value
kafkactl produce my-topic --key=my-key --value=my-value

# the following 3 messages will probably be inserted to different partitions
kafkactl produce my-topic --key=my-key --value=my-value --partitioner=random
kafkactl produce my-topic --key=my-key --value=my-value --partitioner=random
kafkactl produce my-topic --key=my-key --value=my-value --partitioner=random
```
