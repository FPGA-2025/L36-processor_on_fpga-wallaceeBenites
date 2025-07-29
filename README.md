# Executando o microcontrolador numa FPGA

Este laboratório irá encerrar o ciclo de atividades relativas ao processador fazendo com que o seu processador controle os LEDs da placa a partir do código assembly escrito.


## Programando o microcontrolador

Para programar o microcontrolador, siga os passos do laboratório anterior para gerar o arquivo `programa.txt` com as instruções. Use o [RISC-V Online Assembler](https://riscvasm.lucasteske.dev/) e pegue as instruções do campo `Code Hex Dump`.

Nesta atividade usaremos o seguinte código para ser carregado no processador:

```assembly
.global _boot
.section .text

_boot:
    lui t0, 0x80000       # Endereço base do periférico de LEDs
    li t1, 0b00001111     # Valor a ser escrito nos LEDs (acende os 4 primeiros LEDs)
    sw t1, 0(t0)          # Escreve o valor de t1 no registrador de LEDs

loop:
    j loop                # Loop infinito para encerrar o programa
```

> NOTA: A correção irá utilizar esse código específico (coloque o programa.txt na raiz do repositório), mas você tem liberdade para experimentar valores diferentes pra os LEDs.

## Melhorando o código

Por enquanto os LEDs acendem com um valor fixo. E se eles passassem a piscar como nas atividades anteriores?

Para fazer os LEDs piscarem é necessário inserir algum tipo de temporização na execução do processador. Como não temos nenhum relógio disponível, é possível fazer uma temporização executando instruções.

O seguinte loop pode ser criado para fazer o tempo passar:

```assembly
lui a0, 872         # 3 571 712 iterações
addi t0, zero, 0    # contador

timer_loop:
    addi t0, t0, 1
    bne t0, a0, timer_loop
```

A instrução addi leva 4 ciclos para ser executada e a instrução bne leva 3 ciclos. Assim, o total de ciclos para 3 571 712 iterações é 25001984. A 25MHz, esse loop irá demorar cerca de 1 segundo para ser executado.

Faça um novo código assembly com esse temporizador e experimente o novo comportamento do microcontrolador.

## Execução da Atividade

Reúna os arquivos do processador das atividades anteriores e adicione o `programa.txt`. Execute os testes com o script `./run-all.sh`. O resultado será exibido como `OK` em caso de sucesso ou `ERRO` se houver alguma falha.

Se necessário, crie casos de teste adicionais para validar sua implementação.

## Entrega

Primeiro, faça o *push* do seu código no repositório do **GitHub Classroom** para que o código seja carregado na placa. Veja o comportamento no site [embarcatechfpga.lsc.ic.unicamp.br](https://embarcatechfpga.lsc.ic.unicamp.br). Se tiver dúvidas de como utilizar a infraestrutura, veja este guia [aqui](guia_remoto.md).

> **Dica:**
> Não modifique os arquivos de correção. Para entender melhor o funcionamento dos testes, consulte o script `run.sh` disponível no repositório.