<template>
    <div class="calculator">
        <Display :value="displayValue" />
        <Button label="AC" triple @onClick="clearMemory" />
        <Button label="/" operation @onClick="setOperation" />
        <Button label="7" @onClick="addDigit" />
        <Button label="8" @onClick="addDigit" />
        <Button label="9" @onClick="addDigit" />
        <Button label="*" operation @onClick="setOperation" />
        <Button label="4" @onClick="addDigit" />
        <Button label="5" @onClick="addDigit" />
        <Button label="6" @onClick="addDigit" />
        <Button label="-" operation @onClick="setOperation" />
        <Button label="1" @onClick="addDigit" />
        <Button label="2" @onClick="addDigit" />
        <Button label="3" @onClick="addDigit" />
        <Button label="+" operation @onClick="setOperation" />
        <Button label="0" double @onClick="addDigit" />
        <Button label="." @onClick="addDigit" />
        <Button label="=" operation @onClick="setOperation" />

    </div>
</template>

<script>
import Display from "../components/Display"
import Button from "../components/Button"

export default {
    data: function() {
        return{
            displayValue: "0",//valor que será mostrado na tela
            clearDisplay: false,//sempre que o display for atualizado, ele deve ser limpo
            operation: null,//operação que será executada
            values: [0, 0],//armazena os valores que serão usados para realizar a operação
            current: 0//indica qual o valor que será usado para realizar a operação
        }
    },
    components: {
        Display,
        Button
    },
    methods: {
        clearMemory() {
            Object.assign(this.$data, this.$options.data())//Volta o objeto para o estado inicial
            //console.log("Limpar Memória!")
        },
        setOperation(operation) {
            if(this.current === 0) {
                this.operation = operation
                this.current = 1
                this.clearDisplay = true
            } else {
                const equals = operation.label === "="
                const currentOperation = this.operation

                try {
                    this.values[0] = eval(`${this.values[0]} ${currentOperation} ${this.values[1]}`);
                    
                    if (isNaN(this.values[0]) || !isFinite(this.values[0])) {
                    this.clearMemory();
                        return;
                    }
                } catch (e) {
                    this.emit("onError", e)
                }

                this.values[1] = 0//limpa o segundo valor para uma proxima operação

                this.displayValue = this.values[0]//resultado a operação sera mostrado no display
                this.operation = equals ? null : operation
                this.current = equals ? 0 : 1
                this.clearDisplay = !equals
            }
            //console.log("Operação: " + operation)
        },
        addDigit(n) {
            if(n == "." && this.displayValue.includes(".")) {
                return
            }

            const clearDisplay = this.displayValue == "0"
                || this.clearDisplay
            const currentValue = clearDisplay ? "" : this.displayValue//se o display estiver limpo, não precisa concatenar
            const displayValue = currentValue + n//concatena o valor que será mostrado na tela

            this.displayValue = displayValue//atualiza o valor que será mostrado na tela
            this.clearDisplay = false//limpa o display
            
            // Alternativa 1
            this.values[this.current] = displayValue//atualiza o valor que será usado para realizar a operação

            // Alternativa 2
            //if (n !== ".") {
            //    const i = this.current//indica qual o valor que será usado para realizar a operação
            //    const newValue = parseFloat(displayValue)//converte o valor que será usado para realizar a operação
            //    this.values[i] = newValue//atualiza o valor que será usado para realizar a operação
            //}

            //console.log("Número: " + n)
        }
    }
}
</script>

<style>
.calculator {
    height: 275px;
    width: 235px;
    border-radius: 5px;
    overflow: hidden;

    display: grid;
    grid-template-columns: repeat(4, 25%);
    grid-template-rows: 1fr, 48px 48px 48px 48px 48px;
}
</style>