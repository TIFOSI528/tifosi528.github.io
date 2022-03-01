1. decoding related  
*  Kaldi中传统的解码方式需要构建一个相当大的graph，这个graph融合了语言模型、发音词典、上下文相关音素等信息，使用一个相对简单的解码器在graph上进行最优路径的搜索即可；
*  虽然这个方法比较简单，解码速度也很快，但是graph的大小一般超过300Mb，大的甚至有若干Gb，这么大的模型不适合在移动平台部署，模型的灵活性也受到限制
（修改某些词汇的出现的概率就需要重新构图）  

*  因此，Kaldi又先后支持了另外两种解码方式：
>> **1.  two-pass decoding**  
>>> 1. Fast, efficient (relatively small) models are used to generate restrict-sized lattices first.   
>>> 2. They are then rescored with richer knowledge sources (large) for better performance.  
>>> 3. The potential of the two-pass method is limited by the relatively small knowledge sources used in the first pass.  
>>> 4. The two-pass procedure makes the latency issue unavoidable (主要是lattice rescoring步骤导致的延时，more suitable for offline tasks).  
>>>   
>> **2.  on-the-fly composition**  
>>> 1. WFSTs are separated into two (or more) groups and dynamically composed when needed (one-pass decoding).  
>>> 2. 可以减少内存占用；  
>>> 3. is more flexible than offline composition;  
>>> 4. decoding becomes slower since the search space is not optimised;  
>>> 5. 在解码时会产生额外的计算开销(searching & composing);  
>>> 6. lookahead composition特指HCL ∘ G？  
>>> 
>> **3.  BigLM Decoder**  
>>> 1. HCL ∘ G would lose the advantages of having G inside the optimization(when G is composed inside the determinization, it can be thought of as language model lookahead)  
>>> 2. 具体做法：create the decoding graph HCLG with a small (e.g. low-order or pruned) LM, and compose dynamically with a WFST representing the difference between a relatively large LM and the small one.  
>>> 3. The smaller LM can be viewed as the ‘important’ part of the language model, while the Gr stands for the remainder of the language model. --- ‘incremental language model.’  





