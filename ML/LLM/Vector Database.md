#LLM #dataengineering 

# Search

There are tons of vector database providers: Pinecone, Deep Lake, Milvus, Qdrant, Weaviate, ... They all tend to provide similar capabilities with efficient similarity search, optimized storage formats for AI applications, unstructured data accessibility, and cloud-native infrastructure. Most of the game is about how to index billions of vectors for fast retrieval. One such indexing algorithm is Locality-sensitive hashing (LSH).

LSH aims to group vectors together based on similarity. For example, we could partition the vector space into multiple buckets, and we could call “nearest neighbors” whatever vectors belong to the same bucket. In practice, it is done a bit differently. An efficient way to partition the space is to project the vectors onto a space of a specific dimensionality and “binarize“ each component. The projection is done using a random matrix M of dimension (C, R) where C is the dimension of the original vector V and R is the dimension of the space we want to project the vectors into

V' = V. M

For example, if C = 2 and R = 3, we would project from a plane to a 3D space. We can now partition the space with regions above and below the hyperplanes passing by the origin. If we have, for example, a vector A = [0.5, -1.5, 0.3], we look at each of the components and assign a 1 if it is positive and 0 otherwise. The vector A would be hashed to [1, 0, 1] under that process. Every vector assigned the same hash will be close in the vector space and can be labeled “nearest neighbors.” The time complexity to hash a vector V is O(R x C + R) = O(R x C), and retrieving the vectors with the same hash can be done in constant time.

The hash of a vector under the LSH hashing process is a binary vector. To measure how different 2 binary vectors are, we use the Hamming Distance. The Hamming distance counts the number of times 2 strings have different characters. When we have strings of binary numbers, the Hamming distance can be computed using the XOR operation, and the number of resulting 1s can be counted.

![](../../figures/Vector%20Database.png)