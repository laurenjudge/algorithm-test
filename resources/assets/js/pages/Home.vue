<template>
    <div class="container py-3">
        <div class="row">
            <div class="col-12 text-center">
                <div class="form">
                    <div class="form-group row">
                        <div class="col-md-5">
                            <label>Occupation 1</label>
                            <select-occupation v-model="occupation_1"></select-occupation>
                        </div>
                        <div class="col-md-5">
                            <label>Occupation 2</label>
                            <select-occupation v-model="occupation_2"></select-occupation>
                        </div>
                        <div class="col-md-2">
                            <button class="btn btn-danger btn-block mt-4" @click.prevent="compare" :disabled="!occupation_1 || !occupation_2 || loading">
                                <template v-if="loading">
                                    <i class="fa fa-refresh fa-spin"></i>
                                </template>
                                <template v-else>
                                    Compare
                                </template>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <template v-if="match && !loading">
            <div class="col-12 text-center">
                <h1>{{ match }}% match!</h1>
            </div>
            </template>
            <template v-else-if="!match && !loading">
                <div class="col-12 text-center">
                    Please select two Occupations from above and click Compare
                </div>
            </template>
            <template v-else-if="loading">
                <div class="col-12 text-center">
                    Please wait...
                </div>
            </template>
        </div>
    </div>
</template>

<script>
    import SelectOccupation from '../components/form-controls/SelectOccupation';
    export default {
        name: 'home-page',
        components: {
            SelectOccupation
        },
        data() {
            return {
                loading: false,
                occupation_1: null,
                occupation_2: null,
                match: null
            }
        },
        methods: {
            compare() {
                this.loading = true;
                this.axios.post('/api/compare', {
                    occupation_1: this.occupation_1,
                    occupation_2: this.occupation_2
                }).then((response) => {
                    this.loading = false;

                    const job1 = response.data.occupation_1
                    const job2 = response.data.occupation_2

                    this.match = this.calcSimilarity(job1, job2);
                }).catch(() => {
                    this.loading = false;
                });
            },
            calcSimilarity(job1, job2) {
                let denominatorValues = job2.concat(job2);
                let numeratorValues = [];

                 for(const job1Data of job1) {
                    const skillSimilarity = this.findNumeratorValues(job2, job1Data);
                    if(skillSimilarity !== null) {
                        numeratorValues.push(skillSimilarity);
                    }
                }

                let numerator = numeratorValues.reduce((total, difference) => {
                    return total + difference;
                })

                let denominator = denominatorValues.reduce((total, skill) => {
                    return total + skill.value / 100;
                }, 0);

                return Math.round(numerator / denominator);
            },
            findNumeratorValues(job2, job1Data) {
                let skillSimilarity = null;

                for(const job2Data of job2) {
                    let job1Value = Number(job1Data.value);
                    let job2Value = Number(job2Data.value);

                    if(job2Data.label === job1Data.label && job1Value <= job2Value) {
                        skillSimilarity = job1Value / job2Value * 100;
                    } else if(job2Data.label === job1Data.label && job1Value > job2Value) {
                        skillSimilarity = job2Value / job1Value * 100;
                    }   
                }
                return skillSimilarity;
            }
        }
    }
</script>

<style lang="scss" scoped>
    .form-group {
        label {
            font-size: 0.8rem;
            text-align: left;
            display: block;
            margin-bottom: 0.2rem
        }
    }
</style>