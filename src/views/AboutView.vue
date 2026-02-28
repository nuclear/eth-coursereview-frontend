<script setup lang="ts"></script>

<template>
  <main>
    <h1>About</h1>
  </main>
  <div class="ml-4">
    In short we just gather reviews and ratings for ETH courses to help students decide which
    courses to take. Basically trying to complement VVZ with information that is not available
    there. <br />
    We are not affiliated with ETHZ or VSETH. We have reached out to VSETH and they decided that
    they do not want to be involved in this project.
    <v-divider class="mt-5 mb-5" />

    <div>
      <details open>
        <summary>Who are we?</summary>
        <p>
          The two who started this project are Lukas Teufelberger and Andri Hunkeler. And also still
          the ones maintaning it, but in future this will change as we think it should always be run
          by current ETH students.
        </p>
      </details>
      <details>
        <summary>How can I help?</summary>
        <p>There are two ways:</p>
        <p class="ml-4">
          The project is open source, so if you want to change anything in the frontend you can
          always just make a Pullrequest! Also just reach out to us and we can talk about your idea.
        </p>
        <p class="ml-4">
          The other way is to help us accept and reject reviews. We read through every review and if
          any is potentially problematic we discuss it between us, if we should allow it.
        </p>
      </details>
      <details>
        <summary>What does it mean my review is getting reviewed?</summary>
        <p class="ml-4">We take a look at every review to basically make sure:</p>
        <ul class="ml-10">
          <li>It is not spam</li>
          <li>It doesn't just badmouth the lecturer</li>
          <li>And other common sense things that we do not tolerate</li>
          <li>We do not decline reviews because we disagree with them</li>
        </ul>
      </details>
      <details>
        <summary>How does login work?</summary>
        <p>
          We use <a href="https://uniclubs.ch" target="_blank">UniClubs</a> as our authentication
          provider. UniClubs supports SWITCH edu-ID, the Swiss university federation login, so you
          can log in with your existing university account.
        </p>
      </details>
      <details>
        <summary>What data do you save from me?</summary>
        <p>
          When you log in via UniClubs, the only information we receive is a random pseudonymous
          identifier and whether you are a verified student. No name, no email, no personal data.
        </p>
        <p>
          We store this identifier alongside your reviews so that we can block accounts that spam
          us.
        </p>
      </details>
      <details>
        <summary>How can I report a bug?</summary>
        <p>Email us or open an issue on GitHub.</p>
      </details>
      <details>
        <summary>How can I report a review?</summary>
        <p>Currently there is no system but you can just email us.</p>
      </details>
    </div>
  </div>
  <!-- ideas submission? Public? -->
  <!-- bug report: mail? -->
</template>

<style scoped>
details {
  margin-bottom: 10px;
}
</style>
