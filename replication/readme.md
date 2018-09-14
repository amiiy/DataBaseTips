# DataBase Replication

why we need database replications? well becuase:

- Keep data geographically close to client(reduce latency)

- To allow the system to continue working even if some of its parts have failed(and thus increase availablity)

- To scale out the number of machines that can serve read quieries(and thus increase read throughput)

## Leaders and Followers

- One of the replicas is designated the leader (also known as master or primary). when client want to write to the database, they must send their request to the leader, which first write the new data to its local storage.

- The other replicas are known as followers (read replicas, slave, secondaries). Whenever the leader writes new data to its local storage, it also sends the data changes to all of its followes as part of a replication log(known as prolog in mongodb)

- when a client wants to read from the database, it can query either the leader or any of the follower. However. writes are only accepted on the leader(the followers are read-only from the client point of view)

## Synchronous VS Asynchronous Replication

![alt text](https://www.github.com/amiiy/DataBaseTips/blob/master/replication/img1.png 'database replication types')

In the above Figure the replication to followe 1 is synchronous: the leader wait until follower 1 has confirmed that it received the write before reporting success to the user, and before making the write visible to other clients. The replication to follower 2 is asynchronous: the leader send the message, but doesnt wait for a confirm response from the follower.
