<template>
    <div class="progress">
        <div class="progress__container">
            <h2>Reached:</h2>
            <div class="loading">
                <transition appear @before-appear="beforeEnter" @after-appear="enter">
                    <div class="loading__inner" 
                        :style="{'width' : percentage + '%'}"
                        >
                    </div>
                </transition>
                <div class="loading__number" 
                    :style="{'left': percentage + '%'}" 
                    v-if="!done"
                    >
                    ${{ balance }}
                </div>
            </div>
                <p class="goal" 
                    v-bind:class="{done: done}"
                    >
                    <span>Target</span>
                    <strong class="goal__value">${{balanceGoal}}</strong>                
                </p>
        </div>
        <transition>
            <div class="progress__need" 
                v-if="!done"
                >
                <InfoIcon></InfoIcon>
                <span>
                    You need ${{ needToGoal }} more to reach your goal
                </span>                
            </div>
        </transition>

        <p class="error-message" v-if="error">Something went wrong. Try refreshing the page</p>
    </div>
</template>

<script>
    import InfoIcon from './icons/IconInfo.vue';
    export default {
        data () {
            return {
                url: 'https://alex.devel.softservice.org/testapi/',
                balance: null,
                balanceGoal: 15,
                error: null
            }
        },
        created() {
            fetch(this.url)
                .then(response => {
                    if(response.ok) {
                        return response.json()
                    }
                    
                    throw new Error('Something went wrong');
                })
                .then(data => {
                    this.balance = data.balance_usd;
                })
                .then( () => {
                    const interval = setInterval(() => {
                        if(this.balance < this.balanceGoal) {
                            this.balance = Number((this.balance + 0.2).toFixed(1));
                        } else {
                            clearInterval(interval);
                        }
                    }, 2000);
                })
                .catch(error => {
                    this.error = error;
                });
        },
        computed: {
            percentage() {
                return Number(this.balance) / this.balanceGoal *100;
            },

            needToGoal() {
                return (this.balanceGoal - this.balance).toFixed(1);
            },

            done() {
                return this.balance >= this.balanceGoal;
            }
        },
        methods: {
            beforeEnter (el) {
                el.style.width = 0;
            },
            enter (el) {
                el.style.width = `${this.percentage}%`;
                el.style.transition = `width 1.5s linear`;
            }
        },
        components: {
            InfoIcon: InfoIcon
        }
    }
</script>

<style scoped>

    .progress {
        display: flex;
        flex-direction: column;
        place-items: center;
        padding: 15px;
        background-color: #ffffff;
        border: 1px solid var(--medium-gray);
        border-radius: 5px;
    }

    .progress__container {
        display: flex;
        align-items: center;
        width: 80%;
        margin-bottom: 5px;
    }

    .progress__container h2 {
        flex-shrink: 1;
        margin-right: 20px;
        font-size: 16px;
        font-weight: 700;
        color: var(--base-gray)
    }
    .loading {
    position: relative;
    flex-grow: 1;
    flex-shrink: 0;
    height: 15px;
    padding: 3px 5px;
    background-color: var(--progress-background);
  }

  .loading__inner {
    height: 100%;
    background-color: var(--progress-bar);
  }

  .loading__number {
    position: absolute;
    top: 20px;
    display: inline-block;
    padding-top: 10px;
    font-size: 14px;
    font-weight: 700;
    color: var(--base-gray);
    transform: translateX(-65%);
    transition: left 1.5s linear;
  }

  .loading__number::before {
    content: "";
    position: absolute;
    top: 0;
    left: 50%;
    display: block;
    width: 12px;
    height: 8px;
    clip-path: polygon(0 100%, 50% 0, 100% 100%);
    background-color: var(--base-gray);
    transform: translateX(-50%);
  }

  .goal {
    position: relative;
    display: flex;
    flex-direction: column;
    place-items: center;
    margin-left: 2px;
    padding: 0 10px;
    background-color: var(--base-gray);
    color: #ffffff;
    border-radius: 5px;
    transition: background-color 0.4s ease;
    overflow: hidden;
  }

  .goal::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    z-index: 0;
    display: block;
    width: 100%;
    height: 100%;
    background: var(--gray-gradient);
    opacity: 1;
    transition: opacity 0.2s ease;
  }

  .goal.done {
    background-color: var(--success);
  }

  .goal.done::before {
    opacity: 0;
  }

  .goal span {
    position: relative;
    z-index: 1;
    line-height: 150%;
    border-bottom: 1px solid #ffffff;
  }

  .goal__value {
    position: relative;
    z-index: 1;
    font-size: 20px;
    line-height: 150%;
  }

  .progress__need {
    display: flex;
    align-items: center;
    margin-top: 15px;
    color: var(--base-gray);
  }

  .progress__need span {
    margin-left: 5px;
  }

  .error-message {
    margin-top: 10px;
    color: #aa0011;
    font-size: 13px;
    font-weight: 400;
  }
</style>