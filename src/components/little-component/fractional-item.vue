<template>
    <div class="fractional-item" v-bind:class="{'fractional-item--base': statusSign}">
    	<template v-if="statusSign">
			<div class="fractional-item__sign-block">
				<input v-bind:id="'sign-'+uniqueID" class="fractional-item__sign" ref="sign" type="text" v-bind:class="{'fractional-item__sign--error': statusErrorSign}" v-on:input="checkSign">
			</div>
    	</template>

		<div class="fractional-item__content">
			<span class="fractional-item__number-block">
				<input v-bind:id="'numerator-'+uniqueID" class="fractional-item__input" v-bind:class="{'fractional-item__input--error': statusErrorNumerator}" ref="numerator" type="number" v-on:input="changeNumerator">
			</span>
			<span class="fractional-item__number-block">
				<input v-bind:id="'denominator-'+uniqueID" class="fractional-item__input" v-bind:class="{'fractional-item__input--error': statusErrorDenominator}" ref="denominator" type="number" v-on:input="changeDenominator">
			</span>
		</div>
    </div>
</template>

<script>    
		// Генерация уникальных строк для id, name, for
	const uuidv4 = require('uuid/v4');

	export default {
		name: "fractional-item",
		props: {
			"index": Number
		},
		computed: {
				// Уникальная строка, случайно генерируемая через uuidv4
		    uniqueID: function () {
		      	return uuidv4();
		    },
		    statusSign: function () {
		      	return (this.index) ? true : false;
		    },
		},
		data () {
			return {
				sign: "",
				numerator: undefined,
				denominator: undefined,

				statusErrorSign: true,
				statusErrorNumerator: true,
				statusErrorDenominator: true
			};
		},
		mounted () {
			if(!this.index) {
				this.statusErrorSign = false;
			}

            this.$on("error-fractional-item-sign", inf => {
                this.statusErrorNumerator = true;
            }); 
		},
		methods: {
			checkSign () {
				this.sign = this.$refs.sign.value;

				if(!this.sign) {
					this.statusErrorSign = true;
				} else {
					this.checkValueSign();
				}

				this.checkEmitParent();
			},

			checkValueSign () {
				const arr = ["+", "-", "*", "/"];

				if(arr.includes(this.sign)) {
					this.statusErrorSign = false;
				} else {
					this.statusErrorSign = true;
				}
			},

			changeNumerator () {
				this.numerator = this.$refs.numerator.value;

				if(!this.numerator) {
					this.statusErrorNumerator = true;
				} else {
					this.statusErrorNumerator = false;
				}

				this.checkEmitParent();
			},

			changeDenominator () {
				this.denominator = this.$refs.denominator.value;

				if(Number(this.denominator) === 0) {
					this.statusErrorDenominator = true;
				} else {
					this.statusErrorDenominator = false;
				}

				this.checkEmitParent();
			},

			checkEmitParent () {
				let inf = {
					index: this.index,
					numerator: this.numerator,
					denominator: this.denominator,
					sign: this.sign
				};

				if(!this.statusErrorSign && !this.statusErrorNumerator && !this.statusErrorDenominator) {
					inf.status = true;
				} else {
					inf.status = false;
				}

				this.$parent.$emit('change-fractional', inf);
			}
		}
	};
</script>

<style lang=scss>
	.fractional-item {
		&--base {
			display: flex;
    		margin-left: 1rem;
		}

		&__content {
			position: relative;
			display: flex;
			flex-flow: column nowrap;

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

		&__sign-block {
		    position: relative;
		    display: flex;
		    align-items: center;
		    margin-right: 1rem;
		}

		&__number-block {
			margin-bottom: 1.3rem;

			&:last-child {
				margin-bottom: 0px;
			}
		}

		&__sign {
		    width: 40px;
		    border: 1px solid grey;

			&--error {
				border: 1px solid red;
			}
		}

		&__input {
			width: 2rem;
			height: 2rem;
			border: 1px solid grey;

			&--error {
				border: 1px solid red;
			}
		}
	}
</style>