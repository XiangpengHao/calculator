# Bit Calculator 

Online: https://xiangpenghao.github.io/calculator/

![](/dev/screen2.gif)


Allows you to investigate a number in its binary, hexadecimal, and decimal forms. 
Similar to the Windows calculator in programmer mode, but on Web.

Could be helpful to debug memory addresses and bit-packed values, common in system programming. 

Note that every number is **64-bit unsigned integer**, because we system programmers don't use [other types](https://www.reddit.com/r/ProgrammerHumor/comments/13gt6co/standagainstfloats/#lightbox).

Mostly written by ChatGPT.

### Contribution: security and trust
We know Bit Calculator can be the bedrock of humanity, we put [security and trust first](https://gist.github.com/thesamesam/223949d5a074ebc3dce9ee78baad9e27). 
As a result, we implement a novel rule: **any modifications must be originated from ChatGPT**. 

Specifically, please follow this review protocol:
1. You find a problem that (you believe) is real and impactful, and you want to fix it.
2. Send the unmodified source code and the problem to ChatGPT.
3. ChatGPT proposes a solution. Revise your prompt until the solution seems to work. 
4. To simplify the review process, no interactive conversation is allowed, i.e., you have one prompt, and ChatGPT has one response.
5. You (faithfully) implement the solution as instructed by ChatGPT, then send the PR along with the conversation link, i.e., a link from OpenAI.
6. A review ChatGPT will audit your prompt and merge the changes if everything looks secure and safe.

