<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { getFirestore, doc, getDoc, updateDoc, Timestamp } from 'firebase/firestore';
import { useUserStore } from '@/stores/user';
import { useRoute } from 'vue-router';
import type { IInterview, IStage } from '@/interfaces';

const db = getFirestore()
const userStore = useUserStore()
const route = useRoute()

const interview = ref<IInterview>()
const isLoading =ref<boolean>(true)

const docref = doc(db, `users/${userStore.userId}/interviews`, route.params.id as string)

const getData = async (): Promise<void> => {
    isLoading.value = true
    const docSnap = await getDoc(docref)

    if (docSnap.exists()) {
        const data = docSnap.data() as IInterview

        if (data.stages && data.stages.length) {
            data.stages = data.stages.map((stage: IStage) => {
                if (stage.date && stage.date instanceof Timestamp) {
                    return {
                        ...stage,
                        date: stage.date.toDate()
                    }
                }
                return stage
            })
        }
        interview.value = data
    }

    isLoading.value = false
}

const addStage = () => {
    if (interview.value) {
        if (!interview.value.stages) {
            interview.value.stages = []
        }
        interview.value.stages.push({ name: '', date: null, description: '' })
    }
}

const removeStage = (index: number) => {
    if (interview.value) {
        if (interview.value.stages) {
            interview.value.stages.splice(index, 1)
        }
    }
}

const saveInterview = async ():Promise<void> => {
    isLoading.value = true
    await updateDoc(docref, {...interview.value})
    await getData()
    isLoading.value = false
}

onMounted(async() => await getData())
</script>

<template>
    <app-progress class="flex align-items-center h-full" />
    <app-progress v-if="isLoading" />
    <div v-else-if="interview?.id && !isLoading" class="content-interview">
        <app-card>
            <template #title=>Собеседование в компанию {{ interview.company }}</template>
            <template #content>
                <div class="flex flex-column gap-2">
                    <label for="company">Компания</label>
                    <app-input-text class="input mb-3" id="company" v-model="interview.company" />
                </div>
                <div class="flex flex-column gap-2">
                    <label for="vacancyLink">Описание вакансии (ссылка)</label>
                    <app-input-text class="input mb-3" id="vacancyLink" v-model="interview.vacancyLink" />
                </div>
                <div class="flex flex-column gap-2">
                    <label for="hrName">Контакт (имя)</label>
                    <app-input-text class="input mb-3" id="hrName" v-model="interview.hrName" />
                </div>
                <div class="flex flex-column gap-2">
                    <label for="contactTelegram">Telegram username HR</label>
                    <app-input-text class="input mb-3" id="contactTelegram" v-model="interview.contactTelegram" />
                </div>
                <div class="flex flex-column gap-2">
                    <label for="contactWhatsApp">WhatsApp HR</label>
                    <app-input-text class="input mb-3" id="contactWhatsApp" v-model="interview.contactWhatsApp" />
                </div>
                <div class="flex flex-column gap-2">
                    <label for="contactPhone">Телефон HR</label>
                    <app-input-text class="input mb-3" id="contactPhone" v-model="interview.contactPhone" />
                </div>
                <div class="flex flex-wrap gap-2 p-fluid mb-3">
                    <div class="flex flex-auto flex-column gap-2">
                        <label for="salaryFrom">Зарплатная вилка от</label>
                        <app-input-number class="input" inputId="salaryFrom" id="contactPhone" v-model="interview.salaryFrom" />
                    </div>
                    <div class="flex flex-auto flex-column gap-2">
                        <label for="salaryTo">Зарплатная вилка до</label>
                        <app-input-number class="input" inputId="salaryTo" v-model="interview.salaryTo" />
                    </div>
                </div>

                <app-button 
                    label="Добавить этап" 
                    severity="info" 
                    icon="pi pi-plus" 
                    class="mb-3" 
                    @click="addStage"
                ></app-button>
                <template v-if="interview.stages">
                    <div 
                        v-for="(stage, index) in interview.stages" 
                        :key="index"
                        class="interview-stage"
                    >
                        <div class="flex flex-column gap-2">
                            <label :for="`stage-name-${index}`">Название этапа</label>
                            <app-input-text 
                                class="input mb-3" 
                                :id="`stage-name-${index}`" 
                                v-model="stage.name" 
                            />
                        </div>
                        <div class="flex flex-column gap-2">
                            <label :for="`stage-date-${index}`">Дата прохождения этапа</label>
                            <app-calendar 
                                class="input mb-3" 
                                :id="`stage-date-${index}`" 
                                dataFormat="dd.mm.yy" 
                                v-model="stage.date" 
                            />
                        </div>
                        <div class="flex flex-column gap-2">
                            <label :for="`stage-description-${index}`">Комментарий</label>
                            <app-textarea 
                                class="input mb-3" 
                                :id="`stage-description-${index}`" 
                                rows="5" 
                                v-model="stage.description" 
                            />
                        </div>
                        <app-button 
                            label="Удалить этап" 
                            severity="danger" 
                            class="mb-3"
                            @click="removeStage"
                        ></app-button>
                    </div>
                </template>

                <div class="flex flex-wrap gap-3 mb-3">
                    <div class="flex align-items-center">
                        <app-radio 
                            inputId="interviewResult1" 
                            name="result" 
                            value="Refusal" 
                            v-model="interview.result" 
                        />
                        <label for="interviewResult1" class="ml-2">Отказ</label>
                    </div>
                    <div class="flex align-items-center">
                        <app-radio 
                            inputId="interviewResult2" 
                            name="result" 
                            value="Offer" 
                            v-model="interview.result" 
                        />
                        <label for="interviewResult2" class="ml-2">Оффер</label>
                    </div>
                </div>
                <app-button label="Сохранить" icon="pi pi-save" @click="saveInterview"></app-button>
            </template>
        </app-card>
    </div>
</template>

<style scoped>
.content-interview {
    max-width: 600px;
    margin: auto;
}
.input {
    width: 100%;
}
.interview-stage {
    border: 1px solid #ccc;
    border-radius: 6px;
    padding: 10px;
    margin-bottom: 10px;
}
</style>