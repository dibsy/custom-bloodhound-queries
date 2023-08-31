### DCsync

The generic inbuilt query for DCSync in Bloodhound Community is

```cypher
MATCH p=()-[:DCSync|AllExtendedRights|GenericAll]->(:Domain)
RETURN p
```

This returns the following graph


The above query does not return all the other possible paths to carry out DCSync. There query does not include edges with GetChanges and GetChangesAll.
As obtained from BloodHound Exploitating path, DS-Replication-Get-Changes-All permission in conjunction with DS-Replication-Get-Changes, a principal may perform a DCSync attack.

Hence we modify our query a little bit to obtain the same information

```cypher
MATCH p=()-[:DCSync|AllExtendedRights|GenericAll|GetChanges|GetChangesAll]->(:Domain)
RETURN p
```

This returns few more additional nodes, howerver this query shows where atleast one edge ( permission ) is present. The rule as stated above, both permission must exist.

We can narrow it down even further 


```cypher
MATCH (n)-[:GetChangesAll]->(:Domain) WITH n 
MATCH (n)-[:GetChanges]->(:Domain) WITH n
MATCH y=(n)-[]->(:Domain)
RETURN y
```
```cypher
MATCH (b)-[:GetChangesAll]->(:Domain)
WITH b MATCH (b)-[:GetChanges]->(:Domain)
MATCH (p)-[:DCSync|AllExtendedRights|GenericAll]->(:Domain)
MATCH y=(b)-[]->(:Domain)<-[]-(p)
RETURN y
```
<img src="finalquery.png">
