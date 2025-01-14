# Pravega

<v-clicks depth=3>

 - PubSub à la Kafka / Pulsar
   - Streams (topics)
   - Key Value Pair tables 

 - Long term storage (NFS, HDFS, S3)
 - Elastic Streams (topics)
    - Scales up and <span v-mark="{ type: 'underline', color: 'orange', at:'6', delay:2000}">down</span> <span v-mark="{ type: 'underline', color: 'orange', at:'6', delay:1000}">automaticaly</span>
      - Rates (event/s)
      - Volume (Mo/s) 
  - Efficient at <span v-mark="{ type: 'underline', color: 'orange', at:'12'}">high</span> partition counts
  - Exactly once semantics
  - Native transactions

</v-clicks>


---

# Pravega / Streams and Tables

<img src="./images/big-picture.png" alt="Big Picture" width="100%"/>

---

# Pravega



 - PubSub à la Kafka / Pulsar
 - Long term storage (NFS, HDFS, S3)
 - <span v-mark="{ type: 'underline', color: 'orange', at:'2'}">Elastic Streams (topics)</span>
    - Scales up and down automaticaly
      - Rates (event/s)
      - Volume (Mo/s) 
  - Efficient at high partition counts
  - Exactly once semantics
  - Native transactions



---

# Pravega / Auto Scaling

<img src="./images/autoscaling.png" alt="Auto Scaling" width="80%"/>


---

# Pravega / High Partition Counts

<div grid="~ cols-2">
  <div column="2/3">
    <img src="./images/fibers.jpg" alt="Partitions" width="100%"/>
  </div>
<v-clicks depth=3>

   - partitions, millions !
   - high throughput
   - low latency
   - high dynamicity (number of partitions)
</v-clicks>
</div>

---

# Pravega / Consistency Guarantees

### Exactly once semantics

Streams are append only.

<v-clicks depth=3>

 - Durable
 - Consistent
 - Ordered
 - Transactional

</v-clicks>

---

# Pravega / Elastic Streams

<img src="./images/elasticity.png" alt="Elasticity" width="60%"/>

