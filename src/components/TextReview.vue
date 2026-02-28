<script setup lang="ts">
import { pushDeleteReview, pushNewReview, pushUpdateReview } from '@/services/api'
import { computed, ref } from 'vue'
import type { Rating } from './Rating.types'
import { nextTick } from 'vue'
import axios from 'axios'
const {
  editable = false,
  reviewId = -1,
  review,
  isAdd = false,
  ratings,
  semester,
  courseNumber,
  reloadData
} = defineProps<{
  review?: string
  reviewId?: number
  semester?: string
  editable?: boolean
  isAdd?: boolean
  courseNumber?: string
  ratings?: { [key: string]: Rating }
  reloadData?: () => any
}>()

const showSnackbar = ref(false)
const moderationError = ref('')

const emit = defineEmits(['update:review'])

const reviewText = computed({
  get: () => review || '',
  set: (newValue) => {
    emit('update:review', newValue)
  }
})

const isEditing = ref(false)
const old_review = ref(reviewText.value)

if (isAdd) {
  isEditing.value = true
  if (localStorage.text) {
    reviewText.value = localStorage.text
  }
}

function updateValue(e: Event): void {
  if (isAdd) {
    const target = e.target as HTMLTextAreaElement
    localStorage.text = target.value
  }
}

function toggleEdit() {
  isEditing.value = !isEditing.value
  //reset value when canceling
  reviewText.value = old_review.value
}

async function submitEdit() {
  try {
    moderationError.value = ''
    await pushUpdateReview(reviewId, reviewText.value)
    old_review.value = reviewText.value
    isEditing.value = false
    showSnackbar.value = true
  } catch (error) {
    if (axios.isAxiosError(error) && error.response?.status === 422 && error.response?.data?.moderation) {
      moderationError.value = error.response.data.reason || 'Review did not pass automated screening.'
    } else {
      throw error
    }
  }
}

async function deleteReview() {
  await pushDeleteReview(reviewId)
  reviewText.value = ''
  showSnackbar.value = true
  if (reloadData != undefined) {
    nextTick(() => {
      reloadData()
    })
  }
}

async function submitNewReview() {
  console.log('Submit new review')
  if (ratings == undefined || semester == undefined || courseNumber == undefined) {
    console.log('Ratings undefined')
  } else {
    try {
      moderationError.value = ''
      await pushNewReview(reviewText.value, courseNumber, semester, '', ratings)
      reviewText.value = ''
      localStorage.removeItem('text')
      showSnackbar.value = true
      //todo something here: clear ratings, review, semester, courseNumber and show text
      if (reloadData != undefined) {
        reloadData()
      }
    } catch (error) {
      if (axios.isAxiosError(error) && error.response?.status === 422 && error.response?.data?.moderation) {
        moderationError.value = error.response.data.reason || 'Review did not pass automated screening.'
      } else {
        throw error
      }
    }
  }
}
</script>
<template>
  <v-snackbar v-model="showSnackbar" timeout="5000" timer location="top right" max-width="410px">
    Review submitted successfully!
  </v-snackbar>
  <v-card class="border">
    <v-card-text>
      <div v-if="!editable">
        <div v-for="(block, index) in reviewText.split('\n')" :key="index">
          {{ block }}
          <br v-if="block.length == 0" />
        </div>
      </div>
      <v-textarea
        v-else
        v-model="reviewText"
        rows="5"
        auto-grow
        :readonly="!isEditing"
        v-on:input="updateValue"
      ></v-textarea>

      <v-alert v-if="moderationError" type="warning" variant="tonal" class="mt-3" closable @click:close="moderationError = ''">
        {{ moderationError }} Please try to describe what specifically was good or bad rather than just stating an opinion.
      </v-alert>

    </v-card-text>
    <v-card-subtitle v-if="!editable">{{ semester }}</v-card-subtitle>
    <v-card-actions v-if="!isAdd && editable">
      <v-btn variant="flat" color="orange-lighten-1" @click.stop="toggleEdit">{{
        isEditing ? 'Cancel' : 'Edit'
      }}</v-btn>
      <v-btn v-if="isEditing" variant="flat" color="green-lighten-1" @click.stop="submitEdit"
        >Submit</v-btn
      >
      <v-btn variant="flat" color="red-lighten-1" @click.stop="deleteReview">Delete</v-btn>
    </v-card-actions>
    <v-card-actions v-if="isAdd">
      <v-btn
        style="margin-top: 10px"
        variant="flat"
        color="green-lighten-1"
        @click.stop="submitNewReview"
        >Submit</v-btn
      >
    </v-card-actions>
  </v-card>
</template>
