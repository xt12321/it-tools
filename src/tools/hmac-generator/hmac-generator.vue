<script setup lang="ts">
import type { lib } from 'crypto-js';
import {
  HmacMD5,
  HmacRIPEMD160,
  HmacSHA1,
  HmacSHA224,
  HmacSHA256,
  HmacSHA3,
  HmacSHA384,
  HmacSHA512,
  enc,
} from 'crypto-js';

import { convertHexToBin } from '../hash-text/hash-text.service';
import { useCopy } from '@/composable/copy';

const algos = {
  MD5: HmacMD5,
  RIPEMD160: HmacRIPEMD160,
  SHA1: HmacSHA1,
  SHA3: HmacSHA3,
  SHA224: HmacSHA224,
  SHA256: HmacSHA256,
  SHA384: HmacSHA384,
  SHA512: HmacSHA512,
} as const;

type Encoding = keyof typeof enc | 'Bin';

function formatWithEncoding(words: lib.WordArray, encoding: Encoding) {
  if (encoding === 'Bin') {
    return convertHexToBin(words.toString(enc.Hex));
  }
  return words.toString(enc[encoding]);
}

const plainText = ref('');
const secret = ref('');
const hashFunction = ref<keyof typeof algos>('SHA256');
const encoding = ref<Encoding>('Hex');
const hmac = computed(() =>
  formatWithEncoding(algos[hashFunction.value](plainText.value, secret.value), encoding.value),
);
const { copy } = useCopy({ source: hmac, text: "复制成功！" });
</script>

<template>
  <div flex flex-col gap-4>
    <c-input-text v-model:value="plainText" multiline raw-text placeholder="用于计算哈希的文本内容..." rows="3" autosize autofocus label="请输入文本内容" />
    <c-input-text v-model:value="secret" raw-text placeholder="输入密钥..." label="密钥" clearable />

    <div flex gap-2>
      <c-select
        v-model:value="hashFunction" label="哈希函数"
        flex-1
        placeholder="Select an hashing function..."
        :options="Object.keys(algos).map((label) => ({ label, value: label }))"
      />
      <c-select
        v-model:value="encoding" label="输出编码"
        flex-1
        placeholder="选择要输出的编码类型..."
        :options="[
          {
            label: 'Binary (base 2)',
            value: 'Bin',
          },
          {
            label: 'Hexadecimal (base 16)',
            value: 'Hex',
          },
          {
            label: 'Base64 (base 64)',
            value: 'Base64',
          },
          {
            label: 'Base64-url (base 64 with url safe chars)',
            value: 'Base64url',
          },
        ]"
      />
    </div>
    <input-copyable v-model:value="hmac" type="textarea" placeholder="生成的 HMAC 结果..." label="文本生成的HMAC" />
    <div flex justify-center>
      <c-button @click="copy()">
        复制HMAC
      </c-button>
    </div>
  </div>
</template>
