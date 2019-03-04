<template>
	<div class="fractional">
        <div class="fractional__content">
            <template v-for="(item, index) in data">
                <FractionalItem
                    v-bind:ref="fractional(index)"
                    v-bind:index="index"
                    v-bind:key="index"
                ></FractionalItem>
            </template>

            <div class="fractional__result-block">
                <span>=</span>
                <div class="fractional__result">
                    <div class="fractional__result-numerator">
                        {{ resultNumerator }}
                    </div>
                    <div class="fractional__result-denominator">
                        {{ resultDenominator }}
                    </div>
                </div>
            </div>
        </div>

        <div class="fractional__add-block">
            <button class="fractional__add-button" v-on:click.prevent="addFraction">add fraction</button>
        </div>
    </div>
</template>

<script>
        // Подключение компонентов
    import FractionalItem from './little-component/fractional-item.vue';

    const Fraction = require('fractional').Fraction

    export default {
        name: "fractional",
        components: {
            FractionalItem
        },
        data () {
            return {
                length: 2,
                data: [
                    {
                        id: 0
                    },
                    {
                        id: 1
                    }
                ],
                temp: [
                    {
                        index: 0,
                        status: false,
                        numerator: undefined,
                        denominator: undefined,
                        sign: undefined
                    }, 
                    {
                        index: 1,
                        status: false,
                        numerator: undefined,
                        denominator: undefined,
                        sign: undefined
                    }
                ],
                resultNumerator: "",
                resultDenominator: ""
            }
        },
        mounted () {
            this.$on("change-fractional", inf => {
                const _index = inf.index;

                this.temp[_index] = inf;

                if(this.temp.findIndex(x => x.status === false) < 0) {
                    this.changeFractional();
                }
            }); 
        },
        methods: {
            fractional (index) {
                return `fractional${index}`;
            },

            changeFractional () {
                let tempNumerator = this.temp[0].numerator,
                    tempDenominator = this.temp[0].denominator,
                    status = true,
                    statusResult;

                for(let item of this.temp) {
                    if(!item.index) {
                        continue;
                    }

                    status = this.checkSign(tempNumerator, item);

                    if(status) {
                        ({statusResult, tempNumerator, tempDenominator} = this.changeResult(tempNumerator, tempDenominator, item));
                    } else {
                        break;
                    }

                    if(!statusResult) {
                        break;
                    }
                }

                if(status) {
                    this.resultNumerator = tempNumerator;
                    this.resultDenominator = tempDenominator;
                } else {
                    this.resultNumerator = "";
                    this.resultDenominator = "";
                }
            },
            checkSign (tempNumerator, item) {
                let status = false,
                    index,
                    ref;

                if( (Number(item.numerator) !== 0)||(item.sign !== "/") ) {
                    status = true;
                } else {
                    ref = this.$refs[`fractional${item.index}`];
                    index = item.index;

                    ref[0].$emit("error-fractional-item-sign", true);
                    this.temp[index].status = false;
                }

                return status;
            },
            changeResult (tempNumerator, tempDenominator, item) {
                let statusResult = true,
                    denominator,
                    numerator;

                switch(item.sign) {
                    case "+":
                        ({denominator, numerator} = (new Fraction(tempNumerator,tempDenominator)).add(new Fraction(item.numerator,item.denominator)));
                        break;
                    case "-":
                        ({denominator, numerator} = (new Fraction(tempNumerator,tempDenominator)).subtract(new Fraction(item.numerator,item.denominator)));
                        break;
                    case "*":
                        ({denominator, numerator} = (new Fraction(tempNumerator,tempDenominator)).multiply(new Fraction(item.numerator,item.denominator)));
                        break;
                    case "/":
                        ({denominator, numerator} = (new Fraction(tempNumerator,tempDenominator)).divide(new Fraction(item.numerator,item.denominator)));
                        break;
                    default:
                        statusResult = false;
                        console.log("error");
                }

                return {
                    statusResult: statusResult,
                    tempNumerator: numerator,
                    tempDenominator: denominator
                };
            },

            addFraction () {
                this.data.push({
                    id: length
                });

                this.temp.push({
                    index: length,
                    status: false,
                    numerator: undefined,
                    denominator: undefined,
                    sign: undefined
                });

                this.length += 1;
                this.resultNumerator = "";
                this.resultDenominator = "";
            }
        }
    }
</script>

<style lang="scss">
    .fractional {
        position: absolute;
        top: 50%;
        left: 50%;
        display: flex;
        justify-content: center;
        margin-right: -50%;
        transform: translate(-50%, -50%);

        &__content {
            display: flex;
        }

        &__result {
            position: relative;
            margin-left: 1.3rem;

            &:before {
                content: "";
                position: absolute;
                width: 2.5rem;
                height: 1px;
                background: grey;
                top: 50%;
                left: -4px;
            }
        }

        &__result-block {
            display: flex;
            align-items: center;
            width: 40px;
            height: 100%;
            margin-left: 1.3rem;
        }
        
        &__result-numerator,
        &__result-denominator {
            display: flex;
            justify-content: center;
            align-items: center;
            width: 2rem;
            height: 2rem;
            border: 1px solid grey;
        }

        &__result-numerator {
            margin-bottom: 1.3rem;
        }

        &__add-block {
            position: absolute;
            left: 0;
            bottom: -5rem;
        }

        &__add-button {
            width: 10rem;
            padding: 0.5rem;
            border: 1px solid grey;
            background: transparent;
        }
    }
</style>