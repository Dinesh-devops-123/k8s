#RBAC

1.what is RBAC and why do we need it?
2.How k8s users are authenticated and authorized?
3.Types of RBACcomponenets:
  # Role
  # ClusterRole
  # RoleBinding
  # ClusterRoleBinding
4.Creating admin and read-only users,Mapping then using AWS-auth
5.Using different AWS profiles:
  # user1-ro: Read-only access to a single namespace.
  # user2-full: full access to a single namespace to manage everything.
  # user3-admin: full access to everyting in the entire cluster

1.what is RBAC and why do we need it?
 RBAC stands for Role-Based Access control
 It helps us to control who can do what inide a k8s cluster.RBAC helps you limit access based on roles like read-only,developer,admin, etc.

 user must be IAM user and have an valide Service access.

 2.types of RBAC components
  Namespace level (pods,deployments,volumes,services,config,secrets)
        # Role
        # Rolebinding
 k8s cluster
      # cluster
      # clusterRoleBinding

